---
title: Moduł ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak skonfigurować moduł ASP.NET Core na potrzeby hostowania aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 5e04d54bedf7cce8b7a6ab546920e7dec1213a20
ms.sourcegitcommit: d1fa3d69dda675d7a52c7100742dfa6297413376
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86093358"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="af73c-103">Moduł ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af73c-103">ASP.NET Core Module</span></span>

<span data-ttu-id="af73c-104">Autorzy [Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Krzysztof Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [sourabh Shirhatti](https://twitter.com/sshirhatti)i [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="af73c-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="af73c-105">Moduł ASP.NET Core jest natywnym modułem usług IIS, który jest podłączany do potoku usług IIS:</span><span class="sxs-lookup"><span data-stu-id="af73c-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="af73c-106">Hostowanie aplikacji ASP.NET Core w procesie roboczym usług IIS ( `w3wp.exe` ), nazywanym [modelem hostingu w procesie](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="af73c-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="af73c-107">Przekazuj żądania sieci Web do zaplecza ASP.NET Core aplikacji, na której uruchomiono [serwer Kestrel](xref:fundamentals/servers/kestrel), nazywany [modelem hostingu poza procesem](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="af73c-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="af73c-108">Obsługiwane wersje systemu Windows:</span><span class="sxs-lookup"><span data-stu-id="af73c-108">Supported Windows versions:</span></span>

* <span data-ttu-id="af73c-109">System Windows 7 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="af73c-109">Windows 7 or later</span></span>
* <span data-ttu-id="af73c-110">System Windows Server 2012 R2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="af73c-110">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="af73c-111">Podczas hostingu w procesie moduł używa implementacji serwera w procesie dla usług IIS, nazywanego serwerem HTTP IIS ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="af73c-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="af73c-112">Podczas hostingu poza procesem moduł działa tylko z Kestrel.</span><span class="sxs-lookup"><span data-stu-id="af73c-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="af73c-113">Moduł nie działa z [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="af73c-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="af73c-114">Modele hostingu</span><span class="sxs-lookup"><span data-stu-id="af73c-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="af73c-115">Model hostingu w procesie</span><span class="sxs-lookup"><span data-stu-id="af73c-115">In-process hosting model</span></span>

<span data-ttu-id="af73c-116">Domyślnie ASP.NET Core aplikacje do modelu hostingu w procesie.</span><span class="sxs-lookup"><span data-stu-id="af73c-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="af73c-117">Następujące cechy są stosowane podczas hostingu w procesie:</span><span class="sxs-lookup"><span data-stu-id="af73c-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="af73c-118">Serwer HTTP IIS ( `IISHttpServer` ) jest używany zamiast serwera [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="af73c-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="af73c-119">W przypadku [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) wywołań <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> do:</span><span class="sxs-lookup"><span data-stu-id="af73c-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="af73c-120">Zarejestruj `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="af73c-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="af73c-121">Skonfiguruj port i ścieżkę bazową, na której serwer powinien nasłuchiwać przy uruchomionym za modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af73c-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="af73c-122">Skonfiguruj hosta do przechwytywania błędów uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="af73c-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="af73c-123">[Atrybut requestTimeout](#attributes-of-the-aspnetcore-element) nie ma zastosowania do hostingu w procesie.</span><span class="sxs-lookup"><span data-stu-id="af73c-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="af73c-124">Udostępnianie puli aplikacji między aplikacjami nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="af73c-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="af73c-125">Użyj jednej puli aplikacji na aplikację.</span><span class="sxs-lookup"><span data-stu-id="af73c-125">Use one app pool per app.</span></span>

* <span data-ttu-id="af73c-126">W przypadku korzystania z [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) lub ręcznego umieszczania [plikuapp_offline.htm we wdrożeniu](xref:host-and-deploy/iis/index#locked-deployment-files)aplikacja może nie zostać ZAMKNIĘTA natychmiast po otwarciu połączenia.</span><span class="sxs-lookup"><span data-stu-id="af73c-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="af73c-127">Na przykład połączenie protokołu WebSocket może opóźnić zamykanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af73c-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="af73c-128">Architektura (bitową) aplikacji i zainstalowane środowisko uruchomieniowe (x64 lub x86) musi być zgodna z architekturą puli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af73c-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="af73c-129">Wykryto rozłączenia klientów.</span><span class="sxs-lookup"><span data-stu-id="af73c-129">Client disconnects are detected.</span></span> <span data-ttu-id="af73c-130">Token anulowania [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) został anulowany po rozłączeniu klienta.</span><span class="sxs-lookup"><span data-stu-id="af73c-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="af73c-131">W ASP.NET Core 2.2.1 lub wcześniejszym <xref:System.IO.Directory.GetCurrentDirectory*> zwraca katalog procesów roboczych procesu uruchomionego przez usługi IIS, a nie katalog aplikacji (na przykład *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="af73c-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="af73c-132">Przykładowy kod, który ustawia bieżący katalog aplikacji, znajduje się w [klasie CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="af73c-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="af73c-133">Wywołaj `SetCurrentDirectory` metodę.</span><span class="sxs-lookup"><span data-stu-id="af73c-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="af73c-134">Kolejne wywołania w celu <xref:System.IO.Directory.GetCurrentDirectory*> udostępnienia katalogu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af73c-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="af73c-135">Podczas hostingu w procesie <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> nie jest wywoływana wewnętrznie w celu zainicjowania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="af73c-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="af73c-136">W związku z tym, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementacja użyta do przekształcenia oświadczeń po każdym uwierzytelnieniu nie jest domyślnie aktywowana.</span><span class="sxs-lookup"><span data-stu-id="af73c-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="af73c-137">Podczas przekształcania oświadczeń z <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementacją, wywołaj polecenie <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> Dodaj usługi uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="af73c-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="af73c-138">[Wdrożenia pakietów sieci Web (jednego pliku)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) nie są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="af73c-138">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="af73c-139">Model hostingu poza procesem</span><span class="sxs-lookup"><span data-stu-id="af73c-139">Out-of-process hosting model</span></span>

<span data-ttu-id="af73c-140">Aby skonfigurować aplikację do hostingu poza procesem, ustaw wartość `<AspNetCoreHostingModel>` właściwości na `OutOfProcess` w pliku projektu (*. csproj*):</span><span class="sxs-lookup"><span data-stu-id="af73c-140">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="af73c-141">Hosting w procesie jest ustawiony przy użyciu `InProcess` wartości domyślnej.</span><span class="sxs-lookup"><span data-stu-id="af73c-141">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="af73c-142">Wartość `<AspNetCoreHostingModel>` jest uwzględniana wielkość liter, dlatego `inprocess` i `outofprocess` są prawidłowymi wartościami.</span><span class="sxs-lookup"><span data-stu-id="af73c-142">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="af73c-143">Serwer [Kestrel](xref:fundamentals/servers/kestrel) jest używany zamiast serwera http usług IIS ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="af73c-143">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="af73c-144">W przypadku [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) połączeń z <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> :</span><span class="sxs-lookup"><span data-stu-id="af73c-144">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="af73c-145">Skonfiguruj port i ścieżkę bazową, na której serwer powinien nasłuchiwać przy uruchomionym za modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af73c-145">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="af73c-146">Skonfiguruj hosta do przechwytywania błędów uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="af73c-146">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="af73c-147">Zmiany modelu hostingu</span><span class="sxs-lookup"><span data-stu-id="af73c-147">Hosting model changes</span></span>

<span data-ttu-id="af73c-148">Jeśli to `hostingModel` ustawienie zostanie zmienione w pliku *web.config* (wyjaśniono w sekcji [Konfiguracja z web.config](#configuration-with-webconfig) ), moduł odtwarza proces roboczy dla usług IIS.</span><span class="sxs-lookup"><span data-stu-id="af73c-148">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="af73c-149">W przypadku IIS Express moduł nie odtwarza procesu roboczego, ale zamiast tego wyzwala bezpieczne zamknięcie bieżącego procesu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="af73c-149">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="af73c-150">Następne żądanie do aplikacji powoduje zduplikowanie nowego procesu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="af73c-150">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="af73c-151">Nazwa procesu</span><span class="sxs-lookup"><span data-stu-id="af73c-151">Process name</span></span>

<span data-ttu-id="af73c-152">`Process.GetCurrentProcess().ProcessName`Raporty `w3wp` / `iisexpress` (w procesie) lub `dotnet` (pozaprocesowe).</span><span class="sxs-lookup"><span data-stu-id="af73c-152">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="af73c-153">Wiele modułów macierzystych, takich jak uwierzytelnianie systemu Windows, pozostaje aktywnych.</span><span class="sxs-lookup"><span data-stu-id="af73c-153">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="af73c-154">Aby dowiedzieć się więcej na temat modułów usług IIS aktywnych przy użyciu modułu ASP.NET Core, zobacz <xref:host-and-deploy/iis/modules> .</span><span class="sxs-lookup"><span data-stu-id="af73c-154">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="af73c-155">Moduł ASP.NET Core może również:</span><span class="sxs-lookup"><span data-stu-id="af73c-155">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="af73c-156">Ustaw zmienne środowiskowe dla procesu roboczego.</span><span class="sxs-lookup"><span data-stu-id="af73c-156">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="af73c-157">Rejestruj dane wyjściowe stdout do magazynu plików w celu rozwiązywania problemów z uruchamianiem.</span><span class="sxs-lookup"><span data-stu-id="af73c-157">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="af73c-158">Przekazuj tokeny uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="af73c-158">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="af73c-159">Jak zainstalować moduł ASP.NET Core i korzystać z niego</span><span class="sxs-lookup"><span data-stu-id="af73c-159">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="af73c-160">Aby uzyskać instrukcje dotyczące sposobu instalowania modułu ASP.NET Core, zobacz [installing the .NET Core hosting](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="af73c-160">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="af73c-161">Konfiguracja z web.config</span><span class="sxs-lookup"><span data-stu-id="af73c-161">Configuration with web.config</span></span>

<span data-ttu-id="af73c-162">Moduł ASP.NET Core jest skonfigurowany przy użyciu `aspNetCore` sekcji `system.webServer` węzła w pliku *web.config* witryny.</span><span class="sxs-lookup"><span data-stu-id="af73c-162">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="af73c-163">Następujący *web.config* plik jest publikowany dla [wdrożenia zależnego od platformy](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) i konfiguruje moduł ASP.NET Core do obsługi żądań lokacji:</span><span class="sxs-lookup"><span data-stu-id="af73c-163">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="af73c-164">Następujące *web.config* są publikowane dla [wdrożenia samodzielnego](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="af73c-164">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="af73c-165"><xref:System.Configuration.SectionInformation.InheritInChildApplications*>Właściwość jest ustawiona na `false` , aby wskazać, że ustawienia określone w [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) elemencie nie są dziedziczone przez aplikacje, które znajdują się w podkatalogu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af73c-165">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="af73c-166">Gdy aplikacja zostanie wdrożona do [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` ścieżka jest ustawiona na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="af73c-166">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="af73c-167">Ścieżka zapisuje dzienniki stdout do folderu *LogFiles* , który jest lokalizacją automatycznie utworzoną przez usługę.</span><span class="sxs-lookup"><span data-stu-id="af73c-167">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="af73c-168">Aby uzyskać informacje na temat konfiguracji aplikacji podrzędnych usług IIS, zobacz <xref:host-and-deploy/iis/index#sub-applications> .</span><span class="sxs-lookup"><span data-stu-id="af73c-168">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="af73c-169">Atrybuty elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="af73c-169">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="af73c-170">Atrybut</span><span class="sxs-lookup"><span data-stu-id="af73c-170">Attribute</span></span> | <span data-ttu-id="af73c-171">Opis</span><span class="sxs-lookup"><span data-stu-id="af73c-171">Description</span></span> | <span data-ttu-id="af73c-172">Domyślne</span><span class="sxs-lookup"><span data-stu-id="af73c-172">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="af73c-173">Opcjonalny atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="af73c-173">Optional string attribute.</span></span></p><p><span data-ttu-id="af73c-174">Argumenty do pliku wykonywalnego określonego w **processPath**.</span><span class="sxs-lookup"><span data-stu-id="af73c-174">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="af73c-175">Opcjonalny atrybut Boolean.</span><span class="sxs-lookup"><span data-stu-id="af73c-175">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="af73c-176">W przypadku wartości true strona **błędu 502,5 procesu** jest pomijana, a strona kodu stanu 502 skonfigurowana w *web.config* ma pierwszeństwo.</span><span class="sxs-lookup"><span data-stu-id="af73c-176">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="af73c-177">Opcjonalny atrybut Boolean.</span><span class="sxs-lookup"><span data-stu-id="af73c-177">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="af73c-178">Jeśli wartość jest równa true, token jest przekazywany do procesu podrzędnego, który nasłuchuje na% ASPNETCORE_PORT% jako nagłówek "MS-ASPNETCORE-WINAUTHTOKEN" na żądanie.</span><span class="sxs-lookup"><span data-stu-id="af73c-178">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="af73c-179">Jest odpowiedzialny za ten proces, aby wywołać metodę CloseHandle na tym tokenie na żądanie.</span><span class="sxs-lookup"><span data-stu-id="af73c-179">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="af73c-180">Opcjonalny atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="af73c-180">Optional string attribute.</span></span></p><p><span data-ttu-id="af73c-181">Określa model hostingu jako proces ( `InProcess` / `inprocess` ) lub out-of-Process ( `OutOfProcess` / `outofprocess` ).</span><span class="sxs-lookup"><span data-stu-id="af73c-181">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="af73c-182">Opcjonalny atrybut Integer.</span><span class="sxs-lookup"><span data-stu-id="af73c-182">Optional integer attribute.</span></span></p><p><span data-ttu-id="af73c-183">Określa liczbę wystąpień procesu określonego w ustawieniu **processPath** , które może być przypadające na aplikację.</span><span class="sxs-lookup"><span data-stu-id="af73c-183">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="af73c-184">&dagger;W przypadku hostingu w procesie wartość jest ograniczona do `1` .</span><span class="sxs-lookup"><span data-stu-id="af73c-184">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="af73c-185">Ustawienie `processesPerApplication` jest niezalecane.</span><span class="sxs-lookup"><span data-stu-id="af73c-185">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="af73c-186">Ten atrybut zostanie usunięty w przyszłych wydaniach.</span><span class="sxs-lookup"><span data-stu-id="af73c-186">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="af73c-187">Wartooć`1`</span><span class="sxs-lookup"><span data-stu-id="af73c-187">Default: `1`</span></span><br><span data-ttu-id="af73c-188">Długości`1`</span><span class="sxs-lookup"><span data-stu-id="af73c-188">Min: `1`</span></span><br><span data-ttu-id="af73c-189">Maksymalny`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="af73c-189">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="af73c-190">Wymagany atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="af73c-190">Required string attribute.</span></span></p><p><span data-ttu-id="af73c-191">Ścieżka do pliku wykonywalnego, który uruchamia proces nasłuchiwanie żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="af73c-191">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="af73c-192">Obsługiwane są ścieżki względne.</span><span class="sxs-lookup"><span data-stu-id="af73c-192">Relative paths are supported.</span></span> <span data-ttu-id="af73c-193">Jeśli ścieżka zaczyna się od `.` , ścieżka jest uznawana za względną względem katalogu głównego witryny.</span><span class="sxs-lookup"><span data-stu-id="af73c-193">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="af73c-194">Opcjonalny atrybut Integer.</span><span class="sxs-lookup"><span data-stu-id="af73c-194">Optional integer attribute.</span></span></p><p><span data-ttu-id="af73c-195">Określa, ile razy proces określony w **processPath** może ulec awarii na minutę.</span><span class="sxs-lookup"><span data-stu-id="af73c-195">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="af73c-196">W przypadku przekroczenia tego limitu moduł przestaje uruchomić proces przez pozostałą część minuty.</span><span class="sxs-lookup"><span data-stu-id="af73c-196">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="af73c-197">Nieobsługiwane w przypadku hostingu w procesie.</span><span class="sxs-lookup"><span data-stu-id="af73c-197">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="af73c-198">Wartooć`10`</span><span class="sxs-lookup"><span data-stu-id="af73c-198">Default: `10`</span></span><br><span data-ttu-id="af73c-199">Długości`0`</span><span class="sxs-lookup"><span data-stu-id="af73c-199">Min: `0`</span></span><br><span data-ttu-id="af73c-200">Maksymalny`100`</span><span class="sxs-lookup"><span data-stu-id="af73c-200">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="af73c-201">Opcjonalny atrybut TimeSpan.</span><span class="sxs-lookup"><span data-stu-id="af73c-201">Optional timespan attribute.</span></span></p><p><span data-ttu-id="af73c-202">Określa czas, przez który moduł ASP.NET Core czeka na odpowiedź z procesu nasłuchiwania na% ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="af73c-202">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="af73c-203">W wersjach modułu ASP.NET Core, który został dostarczony z wersją ASP.NET Core 2,1 lub nowszą, wartość `requestTimeout` jest określona w godzinach, minutach i sekundach.</span><span class="sxs-lookup"><span data-stu-id="af73c-203">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="af73c-204">Nie dotyczy hostingu w procesie.</span><span class="sxs-lookup"><span data-stu-id="af73c-204">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="af73c-205">W przypadku hostingu w procesie moduł czeka na aplikację w celu przetworzenia żądania.</span><span class="sxs-lookup"><span data-stu-id="af73c-205">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="af73c-206">Prawidłowe wartości segmentów minut i sekund ciągu mieszczą się w zakresie 0-59.</span><span class="sxs-lookup"><span data-stu-id="af73c-206">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="af73c-207">Użycie **60** w wartości minut lub sekund skutkuje *błędem wewnętrznego serwera 500*.</span><span class="sxs-lookup"><span data-stu-id="af73c-207">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="af73c-208">Wartooć`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="af73c-208">Default: `00:02:00`</span></span><br><span data-ttu-id="af73c-209">Długości`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="af73c-209">Min: `00:00:00`</span></span><br><span data-ttu-id="af73c-210">Maksymalny`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="af73c-210">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="af73c-211">Opcjonalny atrybut Integer.</span><span class="sxs-lookup"><span data-stu-id="af73c-211">Optional integer attribute.</span></span></p><p><span data-ttu-id="af73c-212">Czas w sekundach, przez który moduł czeka, aż plik wykonywalny zostanie bezpiecznie zamknięty po wykryciu pliku *app_offline.htm* .</span><span class="sxs-lookup"><span data-stu-id="af73c-212">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="af73c-213">Wartooć`10`</span><span class="sxs-lookup"><span data-stu-id="af73c-213">Default: `10`</span></span><br><span data-ttu-id="af73c-214">Długości`0`</span><span class="sxs-lookup"><span data-stu-id="af73c-214">Min: `0`</span></span><br><span data-ttu-id="af73c-215">Maksymalny`600`</span><span class="sxs-lookup"><span data-stu-id="af73c-215">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="af73c-216">Opcjonalny atrybut Integer.</span><span class="sxs-lookup"><span data-stu-id="af73c-216">Optional integer attribute.</span></span></p><p><span data-ttu-id="af73c-217">Czas w sekundach, przez który moduł czeka, aż plik wykonywalny uruchomi proces nasłuchujący na porcie.</span><span class="sxs-lookup"><span data-stu-id="af73c-217">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="af73c-218">Jeśli ten limit czasu zostanie przekroczony, moduł zakasuje proces.</span><span class="sxs-lookup"><span data-stu-id="af73c-218">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="af73c-219">Moduł podejmuje próbę ponownego uruchomienia procesu, gdy odbierze nowe żądanie i kontynuuje ponowne uruchomienie procesu na kolejnych żądaniach przychodzących, chyba że aplikacja nie będzie mogła uruchomić **rapidFailsPerMinute** liczbę razy w ostatniej minucie.</span><span class="sxs-lookup"><span data-stu-id="af73c-219">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="af73c-220">Wartość 0 (zero) **nie** jest uważana za nieskończony limit czasu.</span><span class="sxs-lookup"><span data-stu-id="af73c-220">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="af73c-221">Wartooć`120`</span><span class="sxs-lookup"><span data-stu-id="af73c-221">Default: `120`</span></span><br><span data-ttu-id="af73c-222">Długości`0`</span><span class="sxs-lookup"><span data-stu-id="af73c-222">Min: `0`</span></span><br><span data-ttu-id="af73c-223">Maksymalny`3600`</span><span class="sxs-lookup"><span data-stu-id="af73c-223">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="af73c-224">Opcjonalny atrybut Boolean.</span><span class="sxs-lookup"><span data-stu-id="af73c-224">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="af73c-225">Jeśli wartość jest równa true, **stdout** i **stderr** dla procesu określonego w **processPath** są przekierowywane do pliku określonego w **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="af73c-225">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="af73c-226">Opcjonalny atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="af73c-226">Optional string attribute.</span></span></p><p><span data-ttu-id="af73c-227">Określa względną lub bezwzględną ścieżkę do pliku, dla którego jest rejestrowany **stdout** i **stderr** z procesu określonego w **processPath** .</span><span class="sxs-lookup"><span data-stu-id="af73c-227">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="af73c-228">Ścieżki względne są względne względem katalogu głównego witryny.</span><span class="sxs-lookup"><span data-stu-id="af73c-228">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="af73c-229">Każda ścieżka rozpoczynająca `.` się od jest określana względem katalogu głównego witryny, a wszystkie inne ścieżki są traktowane jako ścieżki bezwzględne.</span><span class="sxs-lookup"><span data-stu-id="af73c-229">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="af73c-230">Wszystkie foldery podane w ścieżce są tworzone przez moduł po utworzeniu pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="af73c-230">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="af73c-231">Przy użyciu ograniczników podkreślenia, sygnatury czasowej, identyfikatora procesu i rozszerzenia pliku (*log*) są dodawane do ostatniego segmentu ścieżki **stdoutLogFile** .</span><span class="sxs-lookup"><span data-stu-id="af73c-231">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="af73c-232">Jeśli `.\logs\stdout` jest podana jako wartość, przykładowy dziennik stdout jest zapisywany jako *stdout_20180205194132_1934. log* w folderze *Logs* , gdy jest zapisywany na 2/5/2018 o godzinie 19:41:32 przy użyciu identyfikatora procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="af73c-232">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="af73c-233">Ustawianie zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="af73c-233">Set environment variables</span></span>

<span data-ttu-id="af73c-234">Zmienne środowiskowe można określić dla procesu w `processPath` atrybucie.</span><span class="sxs-lookup"><span data-stu-id="af73c-234">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="af73c-235">Określ zmienną środowiskową z `<environmentVariable>` elementem podrzędnym `<environmentVariables>` elementu kolekcji.</span><span class="sxs-lookup"><span data-stu-id="af73c-235">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="af73c-236">Zmienne środowiskowe ustawione w tej sekcji mają pierwszeństwo przed zmiennymi środowiskowymi systemowymi.</span><span class="sxs-lookup"><span data-stu-id="af73c-236">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="af73c-237">W poniższym przykładzie są ustawiane dwie zmienne środowiskowe w *web.config*. `ASPNETCORE_ENVIRONMENT`konfiguruje środowisko aplikacji do programu `Development` .</span><span class="sxs-lookup"><span data-stu-id="af73c-237">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="af73c-238">Deweloper może tymczasowo ustawić tę wartość w pliku *web.config* w celu wymuszenia załadowania [strony wyjątku dewelopera](xref:fundamentals/error-handling) podczas debugowania wyjątku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af73c-238">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="af73c-239">`CONFIG_DIR`to przykład zmiennej środowiskowej zdefiniowanej przez użytkownika, w której deweloper ma napisać kod, który odczytuje wartość przy uruchamianiu, aby utworzyć ścieżkę do ładowania pliku konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af73c-239">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="af73c-240">Alternatywą dla ustawienia środowiska bezpośrednio w *web.config* jest uwzględnienie `<EnvironmentName>` właściwości w [profilu publikacji (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) lub pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="af73c-240">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="af73c-241">To podejście ustawia środowisko w *web.config* po opublikowaniu projektu:</span><span class="sxs-lookup"><span data-stu-id="af73c-241">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="af73c-242">Dla `ASPNETCORE_ENVIRONMENT` zmiennej środowiskowej należy ustawić tylko `Development` serwery przejściowe i testowe, które nie są dostępne dla niezaufanych sieci, takich jak Internet.</span><span class="sxs-lookup"><span data-stu-id="af73c-242">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="af73c-243">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="af73c-243">app_offline.htm</span></span>

<span data-ttu-id="af73c-244">Jeśli plik o nazwie *app_offline.htm* zostanie wykryty w katalogu głównym aplikacji, moduł ASP.NET Core próbuje bezpiecznie zamknąć aplikację i zatrzymać przetwarzanie żądań przychodzących.</span><span class="sxs-lookup"><span data-stu-id="af73c-244">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="af73c-245">Jeśli aplikacja nadal działa po upływie liczby sekund zdefiniowanej w programie `shutdownTimeLimit` , moduł ASP.NET Core kasuje uruchomiony proces.</span><span class="sxs-lookup"><span data-stu-id="af73c-245">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="af73c-246">Gdy plik *app_offline.htm* jest obecny, moduł ASP.NET Core reaguje na żądania, wysyłając z powrotem zawartość pliku *app_offline.htm* .</span><span class="sxs-lookup"><span data-stu-id="af73c-246">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="af73c-247">Po usunięciu pliku *app_offline.htm* następnym żądaniu zostanie uruchomiona aplikacja.</span><span class="sxs-lookup"><span data-stu-id="af73c-247">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="af73c-248">W przypadku korzystania z modelu hostingu poza procesem aplikacja może nie zostać wyłączona natychmiast, jeśli istnieje otwarte połączenie.</span><span class="sxs-lookup"><span data-stu-id="af73c-248">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="af73c-249">Na przykład połączenie protokołu WebSocket może opóźnić zamykanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af73c-249">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="af73c-250">Strona błędu uruchamiania</span><span class="sxs-lookup"><span data-stu-id="af73c-250">Start-up error page</span></span>

<span data-ttu-id="af73c-251">Hosting zarówno w procesie, jak i poza procesem tworzy niestandardowe strony błędów, gdy nie można uruchomić aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af73c-251">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="af73c-252">Jeśli moduł ASP.NET Core nie może odnaleźć programu obsługi żądania w procesie lub out-of-process, zostanie wyświetlona strona kodowa stanu *niepowodzenia ładowania 500,0-procesowego/wyjściowego* .</span><span class="sxs-lookup"><span data-stu-id="af73c-252">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="af73c-253">W przypadku hostingu w procesie, Jeśli uruchomienie aplikacji przez moduł ASP.NET Core nie powiedzie się, zostanie wyświetlona strona kod stanu *niepowodzenia 500,30* .</span><span class="sxs-lookup"><span data-stu-id="af73c-253">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="af73c-254">W przypadku hostingu poza procesem, jeśli moduł ASP.NET Core nie może uruchomić procesu zaplecza lub proces zaplecza zostanie uruchomiony, ale nie nasłuchuje na skonfigurowanym porcie, zostanie wyświetlona strona kod stanu *niepowodzenia procesu 502,5* .</span><span class="sxs-lookup"><span data-stu-id="af73c-254">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="af73c-255">Aby pominąć tę stronę i przywrócić domyślną stronę kodową stanu 5xx usługi IIS, użyj `disableStartUpErrorPage` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="af73c-255">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="af73c-256">Aby uzyskać więcej informacji o konfigurowaniu niestandardowych komunikatów o błędach, zobacz [Błędy \<httpErrors> http ](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="af73c-256">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="af73c-257">Tworzenie i przekierowywanie dzienników</span><span class="sxs-lookup"><span data-stu-id="af73c-257">Log creation and redirection</span></span>

<span data-ttu-id="af73c-258">Moduł ASP.NET Core przekierowuje dane wyjściowe z konsoli stdout i stderr do dysku, jeśli `stdoutLogEnabled` `stdoutLogFile` atrybuty i `aspNetCore` elementu są ustawione.</span><span class="sxs-lookup"><span data-stu-id="af73c-258">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="af73c-259">Wszystkie foldery w `stdoutLogFile` ścieżce są tworzone przez moduł po utworzeniu pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="af73c-259">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="af73c-260">Pula aplikacji musi mieć dostęp do zapisu w lokalizacji, w której zapisano dzienniki (Użyj `IIS AppPool\<app_pool_name>` do zapewnienia uprawnienia do zapisu).</span><span class="sxs-lookup"><span data-stu-id="af73c-260">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="af73c-261">Dzienniki nie są obracane, chyba że zostanie wykonane odtwarzanie procesów/ponowne uruchomienie.</span><span class="sxs-lookup"><span data-stu-id="af73c-261">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="af73c-262">Ponosisz odpowiedzialność dostawcy usług hostingowych, aby ograniczyć ilość miejsca na dysku zużywanej przez dzienniki.</span><span class="sxs-lookup"><span data-stu-id="af73c-262">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="af73c-263">Użycie dziennika stdout jest zalecane tylko w przypadku rozwiązywania problemów z uruchamianiem aplikacji w usługach IIS lub w przypadku korzystania z [obsługi usług IIS w czasie projektowania w programie Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), a nie podczas debugowania lokalnego i uruchamiania aplikacji przy użyciu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="af73c-263">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="af73c-264">Nie używaj dziennika stdout w celu uzyskania ogólnych celów rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af73c-264">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="af73c-265">Aby rejestrować procedury w aplikacji ASP.NET Core, użyj biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki.</span><span class="sxs-lookup"><span data-stu-id="af73c-265">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="af73c-266">Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.</span><span class="sxs-lookup"><span data-stu-id="af73c-266">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="af73c-267">Sygnatura czasowa i rozszerzenie pliku są dodawane automatycznie podczas tworzenia pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="af73c-267">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="af73c-268">Nazwa pliku dziennika składa się przez dołączenie sygnatury czasowej, identyfikatora procesu i rozszerzenia pliku (*log*) do ostatniego segmentu `stdoutLogFile` ścieżki (zazwyczaj *stdout*), które są rozdzielane znakami podkreślenia.</span><span class="sxs-lookup"><span data-stu-id="af73c-268">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="af73c-269">Jeśli `stdoutLogFile` ścieżka kończy się od *stdout*, dziennik aplikacji o identyfikatorze PID 1934 utworzony w 2/5/2018 o 19:42:32 ma nazwę pliku *stdout_20180205194132_1934. log*.</span><span class="sxs-lookup"><span data-stu-id="af73c-269">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="af73c-270">Jeśli `stdoutLogEnabled` ma wartość false, błędy występujące podczas uruchamiania aplikacji są przechwytywane i emitowane do dziennika zdarzeń do 30 KB.</span><span class="sxs-lookup"><span data-stu-id="af73c-270">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="af73c-271">Po uruchomieniu wszystkie dodatkowe dzienniki są odrzucane.</span><span class="sxs-lookup"><span data-stu-id="af73c-271">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="af73c-272">Poniższy przykładowy `aspNetCore` element konfiguruje rejestrowanie stdout w ścieżce względnej `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="af73c-272">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="af73c-273">Upewnij się, że tożsamość użytkownika puli aplikacji ma uprawnienia do zapisu w podanej ścieżce.</span><span class="sxs-lookup"><span data-stu-id="af73c-273">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="af73c-274">W przypadku publikowania aplikacji dla Azure App Service wdrożenia zestaw SDK sieci Web ustawia `stdoutLogFile` wartość na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="af73c-274">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="af73c-275">`%home`Zmienna środowiskowa jest wstępnie zdefiniowana dla aplikacji hostowanych przez Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="af73c-275">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="af73c-276">Aby utworzyć reguły filtru rejestrowania, zobacz sekcje [Konfiguracja](xref:fundamentals/logging/index#log-filtering) i [filtrowanie dzienników](xref:fundamentals/logging/index#log-filtering) w dokumentacji rejestrowania ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af73c-276">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="af73c-277">Aby uzyskać więcej informacji na temat formatów ścieżki, zobacz [formaty ścieżki plików w systemach Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="af73c-277">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="af73c-278">Udoskonalone dzienniki diagnostyczne</span><span class="sxs-lookup"><span data-stu-id="af73c-278">Enhanced diagnostic logs</span></span>

<span data-ttu-id="af73c-279">Moduł ASP.NET Core można skonfigurować w celu udostępnienia dzienników diagnostyki rozszerzonej.</span><span class="sxs-lookup"><span data-stu-id="af73c-279">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="af73c-280">Dodaj `<handlerSettings>` element do `<aspNetCore>` elementu w *web.config*. Ustawienie w `debugLevel` celu `TRACE` ujawniania większej wierności informacji diagnostycznych:</span><span class="sxs-lookup"><span data-stu-id="af73c-280">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="af73c-281">Wszystkie foldery w ścieżce (*dzienniki* w poprzednim przykładzie) są tworzone przez moduł po utworzeniu pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="af73c-281">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="af73c-282">Pula aplikacji musi mieć dostęp do zapisu w lokalizacji, w której zapisano dzienniki (Użyj `IIS AppPool\<app_pool_name>` do zapewnienia uprawnienia do zapisu).</span><span class="sxs-lookup"><span data-stu-id="af73c-282">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="af73c-283">Wartości poziomu debugowania ( `debugLevel` ) mogą zawierać zarówno poziom, jak i lokalizację.</span><span class="sxs-lookup"><span data-stu-id="af73c-283">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="af73c-284">Poziomy (w kolejności od najmniejszej do największej szczegółowości):</span><span class="sxs-lookup"><span data-stu-id="af73c-284">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="af73c-285">BŁĄD</span><span class="sxs-lookup"><span data-stu-id="af73c-285">ERROR</span></span>
* <span data-ttu-id="af73c-286">OSTRZEŻENIE</span><span class="sxs-lookup"><span data-stu-id="af73c-286">WARNING</span></span>
* <span data-ttu-id="af73c-287">INFORMACJE</span><span class="sxs-lookup"><span data-stu-id="af73c-287">INFO</span></span>
* <span data-ttu-id="af73c-288">TRACE</span><span class="sxs-lookup"><span data-stu-id="af73c-288">TRACE</span></span>

<span data-ttu-id="af73c-289">Lokalizacje (wiele lokalizacji jest dozwolonych):</span><span class="sxs-lookup"><span data-stu-id="af73c-289">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="af73c-290">KONSOLI</span><span class="sxs-lookup"><span data-stu-id="af73c-290">CONSOLE</span></span>
* <span data-ttu-id="af73c-291">ELEMENCIE</span><span class="sxs-lookup"><span data-stu-id="af73c-291">EVENTLOG</span></span>
* <span data-ttu-id="af73c-292">PLIK</span><span class="sxs-lookup"><span data-stu-id="af73c-292">FILE</span></span>

<span data-ttu-id="af73c-293">Ustawienia programu obsługi można także zapewnić za pomocą zmiennych środowiskowych:</span><span class="sxs-lookup"><span data-stu-id="af73c-293">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="af73c-294">`ASPNETCORE_MODULE_DEBUG_FILE`: Ścieżka do pliku dziennika debugowania.</span><span class="sxs-lookup"><span data-stu-id="af73c-294">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="af73c-295">(Domyślnie: *aspnetcore-Debug. log*)</span><span class="sxs-lookup"><span data-stu-id="af73c-295">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="af73c-296">`ASPNETCORE_MODULE_DEBUG`: Debugowanie ustawienia poziomu.</span><span class="sxs-lookup"><span data-stu-id="af73c-296">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="af73c-297">**Nie** pozostawiaj włączonej rejestracji debugowania w ramach wdrożenia dłużej niż jest to wymagane, aby rozwiązać problem.</span><span class="sxs-lookup"><span data-stu-id="af73c-297">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="af73c-298">Rozmiar dziennika nie jest ograniczony.</span><span class="sxs-lookup"><span data-stu-id="af73c-298">The size of the log isn't limited.</span></span> <span data-ttu-id="af73c-299">Pozostawienie włączonego dziennika debugowania może spowodować wyczerpanie dostępnego miejsca na dysku i awarię serwera lub usługi App Service.</span><span class="sxs-lookup"><span data-stu-id="af73c-299">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="af73c-300">Zobacz sekcję [Configuration with web.config](#configuration-with-webconfig) , aby zobaczyć przykład `aspNetCore` elementu w pliku *web.config* .</span><span class="sxs-lookup"><span data-stu-id="af73c-300">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="af73c-301">Modyfikowanie rozmiaru stosu</span><span class="sxs-lookup"><span data-stu-id="af73c-301">Modify the stack size</span></span>

<span data-ttu-id="af73c-302">*Stosuje się tylko w przypadku korzystania z modelu hostingu w procesie.*</span><span class="sxs-lookup"><span data-stu-id="af73c-302">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="af73c-303">Skonfiguruj zarządzany rozmiar stosu przy użyciu `stackSize` Ustawienia w bajtach w *web.config*. Domyślny rozmiar to `1048576` bajty (1 MB).</span><span class="sxs-lookup"><span data-stu-id="af73c-303">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="af73c-304">Konfiguracja serwera proxy używa protokołu HTTP i tokenu parowania</span><span class="sxs-lookup"><span data-stu-id="af73c-304">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="af73c-305">*Dotyczy tylko hostingu poza procesem.*</span><span class="sxs-lookup"><span data-stu-id="af73c-305">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="af73c-306">Serwer proxy utworzony między modułem ASP.NET Core a Kestrel używa protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="af73c-306">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="af73c-307">Nie ma ryzyka podsłuchiwanie ruchu między modułem i Kestrel z lokalizacji poza serwerem.</span><span class="sxs-lookup"><span data-stu-id="af73c-307">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="af73c-308">Token parowania jest używany w celu zagwarantowania, że żądania odbierane przez Kestrel zostały przekazane przez usługi IIS i nie pochodzą z innego źródła.</span><span class="sxs-lookup"><span data-stu-id="af73c-308">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="af73c-309">Token parowania jest tworzony i ustawiany jako zmienna środowiskowa ( `ASPNETCORE_TOKEN` ) przez moduł.</span><span class="sxs-lookup"><span data-stu-id="af73c-309">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="af73c-310">Token parowania jest również ustawiany w nagłówku ( `MS-ASPNETCORE-TOKEN` ) na każdym żądaniu z serwerem proxy.</span><span class="sxs-lookup"><span data-stu-id="af73c-310">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="af73c-311">Oprogramowanie pośredniczące usług IIS sprawdza każde odebrane żądanie, aby potwierdzić, że wartość nagłówka tokenu parowania jest zgodna z wartością zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="af73c-311">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="af73c-312">Jeśli wartości tokenu są niezgodne, żądanie zostanie zarejestrowane i odrzucone.</span><span class="sxs-lookup"><span data-stu-id="af73c-312">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="af73c-313">Zmienna środowiskowa tokena parowania i ruch między modułem i Kestrel nie są dostępne z lokalizacji poza serwerem.</span><span class="sxs-lookup"><span data-stu-id="af73c-313">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="af73c-314">Bez znajomości wartości tokenu parowania osoba atakująca nie może przesłać żądań, które pomijają Ewidencjonowanie oprogramowania pośredniczącego usług IIS.</span><span class="sxs-lookup"><span data-stu-id="af73c-314">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="af73c-315">Moduł ASP.NET Core z konfiguracją udostępnioną usług IIS</span><span class="sxs-lookup"><span data-stu-id="af73c-315">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="af73c-316">Instalator modułu ASP.NET Core jest uruchamiany z uprawnieniami konta **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="af73c-316">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="af73c-317">Ponieważ konto systemu lokalnego nie ma uprawnień do modyfikowania dla ścieżki udziału używanej przez udostępnioną konfigurację usług IIS, Instalator zgłasza błąd odmowy dostępu podczas próby skonfigurowania ustawień modułu w pliku *applicationHost.config* w udziale.</span><span class="sxs-lookup"><span data-stu-id="af73c-317">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="af73c-318">W przypadku korzystania z konfiguracji udostępnionej przez usługi IIS na tym samym komputerze, na którym znajduje się instalacja usług IIS, uruchom Instalatora pakietu ASP.NET Core hostowania z `OPT_NO_SHARED_CONFIG_CHECK` parametrem ustawionym na `1` :</span><span class="sxs-lookup"><span data-stu-id="af73c-318">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="af73c-319">Jeśli ścieżka do konfiguracji udostępnionej nie znajduje się na tym samym komputerze, na którym znajduje się instalacja usług IIS, wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="af73c-319">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="af73c-320">Wyłącz konfigurację udostępnioną usług IIS.</span><span class="sxs-lookup"><span data-stu-id="af73c-320">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="af73c-321">Uruchom instalatora.</span><span class="sxs-lookup"><span data-stu-id="af73c-321">Run the installer.</span></span>
1. <span data-ttu-id="af73c-322">Wyeksportuj zaktualizowany plik *applicationHost.config* do udziału.</span><span class="sxs-lookup"><span data-stu-id="af73c-322">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="af73c-323">Włącz ponownie konfigurację udostępnioną usług IIS.</span><span class="sxs-lookup"><span data-stu-id="af73c-323">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="af73c-324">Dzienniki instalacji pakietu i pakietów hostingu</span><span class="sxs-lookup"><span data-stu-id="af73c-324">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="af73c-325">Aby określić wersję zainstalowanego modułu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="af73c-325">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="af73c-326">W systemie hostingu przejdź do *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="af73c-326">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="af73c-327">Znajdź plik *aspnetcore.dll* .</span><span class="sxs-lookup"><span data-stu-id="af73c-327">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="af73c-328">Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Właściwości** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="af73c-328">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="af73c-329">Wybierz kartę **szczegóły** . **Wersja pliku** i **Wersja produktu** reprezentują zainstalowaną wersję modułu.</span><span class="sxs-lookup"><span data-stu-id="af73c-329">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="af73c-330">Dzienniki Instalatora pakietu hostingu dla modułu znajdują się pod adresem *C: \\ Użytkownicy \\ % username% \\ AppData \\ Local \\ temp*. Plik ma nazwę *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="af73c-330">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="af73c-331">Lokalizacje pliku modułu, schematu i konfiguracji</span><span class="sxs-lookup"><span data-stu-id="af73c-331">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="af73c-332">Moduł</span><span class="sxs-lookup"><span data-stu-id="af73c-332">Module</span></span>

<span data-ttu-id="af73c-333">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="af73c-333">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="af73c-334">% windir% \System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="af73c-334">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="af73c-335">% windir% \SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="af73c-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="af73c-336">Module\V2\aspnetcorev2.dll rdzeń%ProgramFiles%\IIS\Asp.Net</span><span class="sxs-lookup"><span data-stu-id="af73c-336">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="af73c-337">% ProgramFiles (x86)% \ IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="af73c-337">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="af73c-338">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="af73c-338">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="af73c-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="af73c-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="af73c-340">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="af73c-340">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="af73c-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="af73c-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="af73c-342">% ProgramFiles (x86)% \ Express\Asp.Net Module\V2\aspnetcorev2.dll Core usług IIS</span><span class="sxs-lookup"><span data-stu-id="af73c-342">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="af73c-343">Schemat</span><span class="sxs-lookup"><span data-stu-id="af73c-343">Schema</span></span>

<span data-ttu-id="af73c-344">**IIS**</span><span class="sxs-lookup"><span data-stu-id="af73c-344">**IIS**</span></span>

* <span data-ttu-id="af73c-345">% windir% \System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="af73c-345">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="af73c-346">% windir% \System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="af73c-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="af73c-347">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="af73c-347">**IIS Express**</span></span>

* <span data-ttu-id="af73c-348">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="af73c-348">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="af73c-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="af73c-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="af73c-350">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="af73c-350">Configuration</span></span>

<span data-ttu-id="af73c-351">**IIS**</span><span class="sxs-lookup"><span data-stu-id="af73c-351">**IIS**</span></span>

* <span data-ttu-id="af73c-352">% windir% \System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="af73c-352">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="af73c-353">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="af73c-353">**IIS Express**</span></span>

* <span data-ttu-id="af73c-354">Visual Studio: {Aplikacja główna} \\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="af73c-354">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="af73c-355">*iisexpress.exe* INTERFEJS WIERSZA POLECENIA:% USERPROFILE% \Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="af73c-355">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="af73c-356">Pliki można znaleźć, wyszukując *aspnetcore* w pliku *applicationHost.config* .</span><span class="sxs-lookup"><span data-stu-id="af73c-356">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="af73c-357">Moduł ASP.NET Core jest natywnym modułem usług IIS, który jest podłączany do potoku usług IIS:</span><span class="sxs-lookup"><span data-stu-id="af73c-357">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="af73c-358">Hostowanie aplikacji ASP.NET Core w procesie roboczym usług IIS ( `w3wp.exe` ), nazywanym [modelem hostingu w procesie](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="af73c-358">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="af73c-359">Przekazuj żądania sieci Web do zaplecza ASP.NET Core aplikacji, na której uruchomiono [serwer Kestrel](xref:fundamentals/servers/kestrel), nazywany [modelem hostingu poza procesem](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="af73c-359">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="af73c-360">Obsługiwane wersje systemu Windows:</span><span class="sxs-lookup"><span data-stu-id="af73c-360">Supported Windows versions:</span></span>

* <span data-ttu-id="af73c-361">System Windows 7 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="af73c-361">Windows 7 or later</span></span>
* <span data-ttu-id="af73c-362">Windows Server 2008 R2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="af73c-362">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="af73c-363">Podczas hostingu w procesie moduł używa implementacji serwera w procesie dla usług IIS, nazywanego serwerem HTTP IIS ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="af73c-363">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="af73c-364">Podczas hostingu poza procesem moduł działa tylko z Kestrel.</span><span class="sxs-lookup"><span data-stu-id="af73c-364">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="af73c-365">Moduł nie działa z [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="af73c-365">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="af73c-366">Modele hostingu</span><span class="sxs-lookup"><span data-stu-id="af73c-366">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="af73c-367">Model hostingu w procesie</span><span class="sxs-lookup"><span data-stu-id="af73c-367">In-process hosting model</span></span>

<span data-ttu-id="af73c-368">Aby skonfigurować aplikację do hostingu w procesie, należy dodać `<AspNetCoreHostingModel>` Właściwość do pliku projektu aplikacji z wartością `InProcess` (hosting poza procesem jest ustawiony z `OutOfProcess` ):</span><span class="sxs-lookup"><span data-stu-id="af73c-368">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="af73c-369">Model hostingu w procesie nie jest obsługiwany w przypadku aplikacji ASP.NET Core przeznaczonych dla .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="af73c-369">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="af73c-370">Wartość `<AspNetCoreHostingModel>` jest uwzględniana wielkość liter, dlatego `inprocess` i `outofprocess` są prawidłowymi wartościami.</span><span class="sxs-lookup"><span data-stu-id="af73c-370">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="af73c-371">Jeśli `<AspNetCoreHostingModel>` Właściwość nie jest obecna w pliku, wartość domyślna to `OutOfProcess` .</span><span class="sxs-lookup"><span data-stu-id="af73c-371">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="af73c-372">Następujące cechy są stosowane podczas hostingu w procesie:</span><span class="sxs-lookup"><span data-stu-id="af73c-372">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="af73c-373">Serwer HTTP IIS ( `IISHttpServer` ) jest używany zamiast serwera [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="af73c-373">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="af73c-374">W przypadku [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) wywołań <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> do:</span><span class="sxs-lookup"><span data-stu-id="af73c-374">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="af73c-375">Zarejestruj `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="af73c-375">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="af73c-376">Skonfiguruj port i ścieżkę bazową, na której serwer powinien nasłuchiwać przy uruchomionym za modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af73c-376">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="af73c-377">Skonfiguruj hosta do przechwytywania błędów uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="af73c-377">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="af73c-378">[Atrybut requestTimeout](#attributes-of-the-aspnetcore-element) nie ma zastosowania do hostingu w procesie.</span><span class="sxs-lookup"><span data-stu-id="af73c-378">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="af73c-379">Udostępnianie puli aplikacji między aplikacjami nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="af73c-379">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="af73c-380">Użyj jednej puli aplikacji na aplikację.</span><span class="sxs-lookup"><span data-stu-id="af73c-380">Use one app pool per app.</span></span>

* <span data-ttu-id="af73c-381">W przypadku korzystania z [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) lub ręcznego umieszczania [plikuapp_offline.htm we wdrożeniu](xref:host-and-deploy/iis/index#locked-deployment-files)aplikacja może nie zostać ZAMKNIĘTA natychmiast po otwarciu połączenia.</span><span class="sxs-lookup"><span data-stu-id="af73c-381">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="af73c-382">Na przykład połączenie protokołu WebSocket może opóźnić zamykanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af73c-382">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="af73c-383">Architektura (bitową) aplikacji i zainstalowane środowisko uruchomieniowe (x64 lub x86) musi być zgodna z architekturą puli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af73c-383">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="af73c-384">Wykryto rozłączenia klientów.</span><span class="sxs-lookup"><span data-stu-id="af73c-384">Client disconnects are detected.</span></span> <span data-ttu-id="af73c-385">Token anulowania [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) został anulowany po rozłączeniu klienta.</span><span class="sxs-lookup"><span data-stu-id="af73c-385">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="af73c-386">W ASP.NET Core 2.2.1 lub wcześniejszym <xref:System.IO.Directory.GetCurrentDirectory*> zwraca katalog procesów roboczych procesu uruchomionego przez usługi IIS, a nie katalog aplikacji (na przykład *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="af73c-386">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="af73c-387">Przykładowy kod, który ustawia bieżący katalog aplikacji, znajduje się w [klasie CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="af73c-387">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="af73c-388">Wywołaj `SetCurrentDirectory` metodę.</span><span class="sxs-lookup"><span data-stu-id="af73c-388">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="af73c-389">Kolejne wywołania w celu <xref:System.IO.Directory.GetCurrentDirectory*> udostępnienia katalogu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af73c-389">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="af73c-390">Podczas hostingu w procesie <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> nie jest wywoływana wewnętrznie w celu zainicjowania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="af73c-390">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="af73c-391">W związku z tym, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementacja użyta do przekształcenia oświadczeń po każdym uwierzytelnieniu nie jest domyślnie aktywowana.</span><span class="sxs-lookup"><span data-stu-id="af73c-391">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="af73c-392">Podczas przekształcania oświadczeń z <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementacją, wywołaj polecenie <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> Dodaj usługi uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="af73c-392">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="af73c-393">Model hostingu poza procesem</span><span class="sxs-lookup"><span data-stu-id="af73c-393">Out-of-process hosting model</span></span>

<span data-ttu-id="af73c-394">Aby skonfigurować aplikację do hostingu poza procesem, użyj jednego z następujących metod w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="af73c-394">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="af73c-395">Nie określaj `<AspNetCoreHostingModel>` właściwości.</span><span class="sxs-lookup"><span data-stu-id="af73c-395">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="af73c-396">Jeśli `<AspNetCoreHostingModel>` Właściwość nie jest obecna w pliku, wartość domyślna to `OutOfProcess` .</span><span class="sxs-lookup"><span data-stu-id="af73c-396">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="af73c-397">Ustaw wartość `<AspNetCoreHostingModel>` właściwości na `OutOfProcess` (hosting w procesie jest ustawiany z `InProcess` ):</span><span class="sxs-lookup"><span data-stu-id="af73c-397">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="af73c-398">W tej wartości wielkość liter nie jest rozróżniana `inprocess` i `outofprocess` są prawidłowe wartości.</span><span class="sxs-lookup"><span data-stu-id="af73c-398">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="af73c-399">Serwer [Kestrel](xref:fundamentals/servers/kestrel) jest używany zamiast serwera http usług IIS ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="af73c-399">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="af73c-400">W przypadku [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) połączeń z <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> :</span><span class="sxs-lookup"><span data-stu-id="af73c-400">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="af73c-401">Skonfiguruj port i ścieżkę bazową, na której serwer powinien nasłuchiwać przy uruchomionym za modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af73c-401">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="af73c-402">Skonfiguruj hosta do przechwytywania błędów uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="af73c-402">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="af73c-403">Zmiany modelu hostingu</span><span class="sxs-lookup"><span data-stu-id="af73c-403">Hosting model changes</span></span>

<span data-ttu-id="af73c-404">Jeśli to `hostingModel` ustawienie zostanie zmienione w pliku *web.config* (wyjaśniono w sekcji [Konfiguracja z web.config](#configuration-with-webconfig) ), moduł odtwarza proces roboczy dla usług IIS.</span><span class="sxs-lookup"><span data-stu-id="af73c-404">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="af73c-405">W przypadku IIS Express moduł nie odtwarza procesu roboczego, ale zamiast tego wyzwala bezpieczne zamknięcie bieżącego procesu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="af73c-405">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="af73c-406">Następne żądanie do aplikacji powoduje zduplikowanie nowego procesu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="af73c-406">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="af73c-407">Nazwa procesu</span><span class="sxs-lookup"><span data-stu-id="af73c-407">Process name</span></span>

<span data-ttu-id="af73c-408">`Process.GetCurrentProcess().ProcessName`Raporty `w3wp` / `iisexpress` (w procesie) lub `dotnet` (pozaprocesowe).</span><span class="sxs-lookup"><span data-stu-id="af73c-408">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="af73c-409">Wiele modułów macierzystych, takich jak uwierzytelnianie systemu Windows, pozostaje aktywnych.</span><span class="sxs-lookup"><span data-stu-id="af73c-409">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="af73c-410">Aby dowiedzieć się więcej na temat modułów usług IIS aktywnych przy użyciu modułu ASP.NET Core, zobacz <xref:host-and-deploy/iis/modules> .</span><span class="sxs-lookup"><span data-stu-id="af73c-410">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="af73c-411">Moduł ASP.NET Core może również:</span><span class="sxs-lookup"><span data-stu-id="af73c-411">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="af73c-412">Ustaw zmienne środowiskowe dla procesu roboczego.</span><span class="sxs-lookup"><span data-stu-id="af73c-412">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="af73c-413">Rejestruj dane wyjściowe stdout do magazynu plików w celu rozwiązywania problemów z uruchamianiem.</span><span class="sxs-lookup"><span data-stu-id="af73c-413">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="af73c-414">Przekazuj tokeny uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="af73c-414">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="af73c-415">Jak zainstalować moduł ASP.NET Core i korzystać z niego</span><span class="sxs-lookup"><span data-stu-id="af73c-415">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="af73c-416">Aby uzyskać instrukcje dotyczące sposobu instalowania modułu ASP.NET Core, zobacz [installing the .NET Core hosting](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="af73c-416">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="af73c-417">Konfiguracja z web.config</span><span class="sxs-lookup"><span data-stu-id="af73c-417">Configuration with web.config</span></span>

<span data-ttu-id="af73c-418">Moduł ASP.NET Core jest skonfigurowany przy użyciu `aspNetCore` sekcji `system.webServer` węzła w pliku *web.config* witryny.</span><span class="sxs-lookup"><span data-stu-id="af73c-418">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="af73c-419">Następujący *web.config* plik jest publikowany dla [wdrożenia zależnego od platformy](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) i konfiguruje moduł ASP.NET Core do obsługi żądań lokacji:</span><span class="sxs-lookup"><span data-stu-id="af73c-419">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="af73c-420">Następujące *web.config* są publikowane dla [wdrożenia samodzielnego](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="af73c-420">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="af73c-421"><xref:System.Configuration.SectionInformation.InheritInChildApplications*>Właściwość jest ustawiona na `false` , aby wskazać, że ustawienia określone w [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) elemencie nie są dziedziczone przez aplikacje, które znajdują się w podkatalogu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af73c-421">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="af73c-422">Gdy aplikacja zostanie wdrożona do [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` ścieżka jest ustawiona na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="af73c-422">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="af73c-423">Ścieżka zapisuje dzienniki stdout do folderu *LogFiles* , który jest lokalizacją automatycznie utworzoną przez usługę.</span><span class="sxs-lookup"><span data-stu-id="af73c-423">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="af73c-424">Aby uzyskać informacje na temat konfiguracji aplikacji podrzędnych usług IIS, zobacz <xref:host-and-deploy/iis/index#sub-applications> .</span><span class="sxs-lookup"><span data-stu-id="af73c-424">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="af73c-425">Atrybuty elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="af73c-425">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="af73c-426">Atrybut</span><span class="sxs-lookup"><span data-stu-id="af73c-426">Attribute</span></span> | <span data-ttu-id="af73c-427">Opis</span><span class="sxs-lookup"><span data-stu-id="af73c-427">Description</span></span> | <span data-ttu-id="af73c-428">Domyślne</span><span class="sxs-lookup"><span data-stu-id="af73c-428">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="af73c-429">Opcjonalny atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="af73c-429">Optional string attribute.</span></span></p><p><span data-ttu-id="af73c-430">Argumenty do pliku wykonywalnego określonego w **processPath**.</span><span class="sxs-lookup"><span data-stu-id="af73c-430">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="af73c-431">Opcjonalny atrybut Boolean.</span><span class="sxs-lookup"><span data-stu-id="af73c-431">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="af73c-432">W przypadku wartości true strona **błędu 502,5 procesu** jest pomijana, a strona kodu stanu 502 skonfigurowana w *web.config* ma pierwszeństwo.</span><span class="sxs-lookup"><span data-stu-id="af73c-432">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="af73c-433">Opcjonalny atrybut Boolean.</span><span class="sxs-lookup"><span data-stu-id="af73c-433">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="af73c-434">Jeśli wartość jest równa true, token jest przekazywany do procesu podrzędnego, który nasłuchuje na% ASPNETCORE_PORT% jako nagłówek "MS-ASPNETCORE-WINAUTHTOKEN" na żądanie.</span><span class="sxs-lookup"><span data-stu-id="af73c-434">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="af73c-435">Jest odpowiedzialny za ten proces, aby wywołać metodę CloseHandle na tym tokenie na żądanie.</span><span class="sxs-lookup"><span data-stu-id="af73c-435">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="af73c-436">Opcjonalny atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="af73c-436">Optional string attribute.</span></span></p><p><span data-ttu-id="af73c-437">Określa model hostingu jako proces ( `InProcess` / `inprocess` ) lub out-of-Process ( `OutOfProcess` / `outofprocess` ).</span><span class="sxs-lookup"><span data-stu-id="af73c-437">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="af73c-438">Opcjonalny atrybut Integer.</span><span class="sxs-lookup"><span data-stu-id="af73c-438">Optional integer attribute.</span></span></p><p><span data-ttu-id="af73c-439">Określa liczbę wystąpień procesu określonego w ustawieniu **processPath** , które może być przypadające na aplikację.</span><span class="sxs-lookup"><span data-stu-id="af73c-439">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="af73c-440">&dagger;W przypadku hostingu w procesie wartość jest ograniczona do `1` .</span><span class="sxs-lookup"><span data-stu-id="af73c-440">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="af73c-441">Ustawienie `processesPerApplication` jest niezalecane.</span><span class="sxs-lookup"><span data-stu-id="af73c-441">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="af73c-442">Ten atrybut zostanie usunięty w przyszłych wydaniach.</span><span class="sxs-lookup"><span data-stu-id="af73c-442">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="af73c-443">Wartooć`1`</span><span class="sxs-lookup"><span data-stu-id="af73c-443">Default: `1`</span></span><br><span data-ttu-id="af73c-444">Długości`1`</span><span class="sxs-lookup"><span data-stu-id="af73c-444">Min: `1`</span></span><br><span data-ttu-id="af73c-445">Maksymalny`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="af73c-445">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="af73c-446">Wymagany atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="af73c-446">Required string attribute.</span></span></p><p><span data-ttu-id="af73c-447">Ścieżka do pliku wykonywalnego, który uruchamia proces nasłuchiwanie żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="af73c-447">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="af73c-448">Obsługiwane są ścieżki względne.</span><span class="sxs-lookup"><span data-stu-id="af73c-448">Relative paths are supported.</span></span> <span data-ttu-id="af73c-449">Jeśli ścieżka zaczyna się od `.` , ścieżka jest uznawana za względną względem katalogu głównego witryny.</span><span class="sxs-lookup"><span data-stu-id="af73c-449">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="af73c-450">Opcjonalny atrybut Integer.</span><span class="sxs-lookup"><span data-stu-id="af73c-450">Optional integer attribute.</span></span></p><p><span data-ttu-id="af73c-451">Określa, ile razy proces określony w **processPath** może ulec awarii na minutę.</span><span class="sxs-lookup"><span data-stu-id="af73c-451">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="af73c-452">W przypadku przekroczenia tego limitu moduł przestaje uruchomić proces przez pozostałą część minuty.</span><span class="sxs-lookup"><span data-stu-id="af73c-452">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="af73c-453">Nieobsługiwane w przypadku hostingu w procesie.</span><span class="sxs-lookup"><span data-stu-id="af73c-453">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="af73c-454">Wartooć`10`</span><span class="sxs-lookup"><span data-stu-id="af73c-454">Default: `10`</span></span><br><span data-ttu-id="af73c-455">Długości`0`</span><span class="sxs-lookup"><span data-stu-id="af73c-455">Min: `0`</span></span><br><span data-ttu-id="af73c-456">Maksymalny`100`</span><span class="sxs-lookup"><span data-stu-id="af73c-456">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="af73c-457">Opcjonalny atrybut TimeSpan.</span><span class="sxs-lookup"><span data-stu-id="af73c-457">Optional timespan attribute.</span></span></p><p><span data-ttu-id="af73c-458">Określa czas, przez który moduł ASP.NET Core czeka na odpowiedź z procesu nasłuchiwania na% ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="af73c-458">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="af73c-459">W wersjach modułu ASP.NET Core, który został dostarczony z wersją ASP.NET Core 2,1 lub nowszą, wartość `requestTimeout` jest określona w godzinach, minutach i sekundach.</span><span class="sxs-lookup"><span data-stu-id="af73c-459">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="af73c-460">Nie dotyczy hostingu w procesie.</span><span class="sxs-lookup"><span data-stu-id="af73c-460">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="af73c-461">W przypadku hostingu w procesie moduł czeka na aplikację w celu przetworzenia żądania.</span><span class="sxs-lookup"><span data-stu-id="af73c-461">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="af73c-462">Prawidłowe wartości segmentów minut i sekund ciągu mieszczą się w zakresie 0-59.</span><span class="sxs-lookup"><span data-stu-id="af73c-462">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="af73c-463">Użycie **60** w wartości minut lub sekund skutkuje *błędem wewnętrznego serwera 500*.</span><span class="sxs-lookup"><span data-stu-id="af73c-463">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="af73c-464">Wartooć`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="af73c-464">Default: `00:02:00`</span></span><br><span data-ttu-id="af73c-465">Długości`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="af73c-465">Min: `00:00:00`</span></span><br><span data-ttu-id="af73c-466">Maksymalny`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="af73c-466">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="af73c-467">Opcjonalny atrybut Integer.</span><span class="sxs-lookup"><span data-stu-id="af73c-467">Optional integer attribute.</span></span></p><p><span data-ttu-id="af73c-468">Czas w sekundach, przez który moduł czeka, aż plik wykonywalny zostanie bezpiecznie zamknięty po wykryciu pliku *app_offline.htm* .</span><span class="sxs-lookup"><span data-stu-id="af73c-468">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="af73c-469">Wartooć`10`</span><span class="sxs-lookup"><span data-stu-id="af73c-469">Default: `10`</span></span><br><span data-ttu-id="af73c-470">Długości`0`</span><span class="sxs-lookup"><span data-stu-id="af73c-470">Min: `0`</span></span><br><span data-ttu-id="af73c-471">Maksymalny`600`</span><span class="sxs-lookup"><span data-stu-id="af73c-471">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="af73c-472">Opcjonalny atrybut Integer.</span><span class="sxs-lookup"><span data-stu-id="af73c-472">Optional integer attribute.</span></span></p><p><span data-ttu-id="af73c-473">Czas w sekundach, przez który moduł czeka, aż plik wykonywalny uruchomi proces nasłuchujący na porcie.</span><span class="sxs-lookup"><span data-stu-id="af73c-473">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="af73c-474">Jeśli ten limit czasu zostanie przekroczony, moduł zakasuje proces.</span><span class="sxs-lookup"><span data-stu-id="af73c-474">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="af73c-475">Moduł podejmuje próbę ponownego uruchomienia procesu, gdy odbierze nowe żądanie i kontynuuje ponowne uruchomienie procesu na kolejnych żądaniach przychodzących, chyba że aplikacja nie będzie mogła uruchomić **rapidFailsPerMinute** liczbę razy w ostatniej minucie.</span><span class="sxs-lookup"><span data-stu-id="af73c-475">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="af73c-476">Wartość 0 (zero) **nie** jest uważana za nieskończony limit czasu.</span><span class="sxs-lookup"><span data-stu-id="af73c-476">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="af73c-477">Wartooć`120`</span><span class="sxs-lookup"><span data-stu-id="af73c-477">Default: `120`</span></span><br><span data-ttu-id="af73c-478">Długości`0`</span><span class="sxs-lookup"><span data-stu-id="af73c-478">Min: `0`</span></span><br><span data-ttu-id="af73c-479">Maksymalny`3600`</span><span class="sxs-lookup"><span data-stu-id="af73c-479">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="af73c-480">Opcjonalny atrybut Boolean.</span><span class="sxs-lookup"><span data-stu-id="af73c-480">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="af73c-481">Jeśli wartość jest równa true, **stdout** i **stderr** dla procesu określonego w **processPath** są przekierowywane do pliku określonego w **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="af73c-481">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="af73c-482">Opcjonalny atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="af73c-482">Optional string attribute.</span></span></p><p><span data-ttu-id="af73c-483">Określa względną lub bezwzględną ścieżkę do pliku, dla którego jest rejestrowany **stdout** i **stderr** z procesu określonego w **processPath** .</span><span class="sxs-lookup"><span data-stu-id="af73c-483">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="af73c-484">Ścieżki względne są względne względem katalogu głównego witryny.</span><span class="sxs-lookup"><span data-stu-id="af73c-484">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="af73c-485">Każda ścieżka rozpoczynająca `.` się od jest określana względem katalogu głównego witryny, a wszystkie inne ścieżki są traktowane jako ścieżki bezwzględne.</span><span class="sxs-lookup"><span data-stu-id="af73c-485">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="af73c-486">Wszystkie foldery podane w ścieżce są tworzone przez moduł po utworzeniu pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="af73c-486">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="af73c-487">Przy użyciu ograniczników podkreślenia, sygnatury czasowej, identyfikatora procesu i rozszerzenia pliku (*log*) są dodawane do ostatniego segmentu ścieżki **stdoutLogFile** .</span><span class="sxs-lookup"><span data-stu-id="af73c-487">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="af73c-488">Jeśli `.\logs\stdout` jest podana jako wartość, przykładowy dziennik stdout jest zapisywany jako *stdout_20180205194132_1934. log* w folderze *Logs* , gdy jest zapisywany na 2/5/2018 o godzinie 19:41:32 przy użyciu identyfikatora procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="af73c-488">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="af73c-489">Ustawianie zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="af73c-489">Setting environment variables</span></span>

<span data-ttu-id="af73c-490">Zmienne środowiskowe można określić dla procesu w `processPath` atrybucie.</span><span class="sxs-lookup"><span data-stu-id="af73c-490">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="af73c-491">Określ zmienną środowiskową z `<environmentVariable>` elementem podrzędnym `<environmentVariables>` elementu kolekcji.</span><span class="sxs-lookup"><span data-stu-id="af73c-491">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="af73c-492">Zmienne środowiskowe ustawione w tej sekcji mają pierwszeństwo przed zmiennymi środowiskowymi systemowymi.</span><span class="sxs-lookup"><span data-stu-id="af73c-492">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="af73c-493">W poniższym przykładzie są ustawiane dwie zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="af73c-493">The following example sets two environment variables.</span></span> <span data-ttu-id="af73c-494">`ASPNETCORE_ENVIRONMENT`konfiguruje środowisko aplikacji do programu `Development` .</span><span class="sxs-lookup"><span data-stu-id="af73c-494">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="af73c-495">Deweloper może tymczasowo ustawić tę wartość w pliku *web.config* w celu wymuszenia załadowania [strony wyjątku dewelopera](xref:fundamentals/error-handling) podczas debugowania wyjątku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af73c-495">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="af73c-496">`CONFIG_DIR`to przykład zmiennej środowiskowej zdefiniowanej przez użytkownika, w której deweloper ma napisać kod, który odczytuje wartość przy uruchamianiu, aby utworzyć ścieżkę do ładowania pliku konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af73c-496">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="af73c-497">Alternatywą dla ustawienia środowiska bezpośrednio w *web.config* jest uwzględnienie `<EnvironmentName>` właściwości w [profilu publikacji (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) lub pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="af73c-497">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="af73c-498">To podejście ustawia środowisko w *web.config* po opublikowaniu projektu:</span><span class="sxs-lookup"><span data-stu-id="af73c-498">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="af73c-499">Dla `ASPNETCORE_ENVIRONMENT` zmiennej środowiskowej należy ustawić tylko `Development` serwery przejściowe i testowe, które nie są dostępne dla niezaufanych sieci, takich jak Internet.</span><span class="sxs-lookup"><span data-stu-id="af73c-499">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="af73c-500">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="af73c-500">app_offline.htm</span></span>

<span data-ttu-id="af73c-501">Jeśli plik o nazwie *app_offline.htm* zostanie wykryty w katalogu głównym aplikacji, moduł ASP.NET Core próbuje bezpiecznie zamknąć aplikację i zatrzymać przetwarzanie żądań przychodzących.</span><span class="sxs-lookup"><span data-stu-id="af73c-501">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="af73c-502">Jeśli aplikacja nadal działa po upływie liczby sekund zdefiniowanej w programie `shutdownTimeLimit` , moduł ASP.NET Core kasuje uruchomiony proces.</span><span class="sxs-lookup"><span data-stu-id="af73c-502">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="af73c-503">Gdy plik *app_offline.htm* jest obecny, moduł ASP.NET Core reaguje na żądania, wysyłając z powrotem zawartość pliku *app_offline.htm* .</span><span class="sxs-lookup"><span data-stu-id="af73c-503">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="af73c-504">Po usunięciu pliku *app_offline.htm* następnym żądaniu zostanie uruchomiona aplikacja.</span><span class="sxs-lookup"><span data-stu-id="af73c-504">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="af73c-505">W przypadku korzystania z modelu hostingu poza procesem aplikacja może nie zostać wyłączona natychmiast, jeśli istnieje otwarte połączenie.</span><span class="sxs-lookup"><span data-stu-id="af73c-505">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="af73c-506">Na przykład połączenie protokołu WebSocket może opóźnić zamykanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af73c-506">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="af73c-507">Strona błędu uruchamiania</span><span class="sxs-lookup"><span data-stu-id="af73c-507">Start-up error page</span></span>

<span data-ttu-id="af73c-508">Hosting zarówno w procesie, jak i poza procesem tworzy niestandardowe strony błędów, gdy nie można uruchomić aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af73c-508">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="af73c-509">Jeśli moduł ASP.NET Core nie może odnaleźć programu obsługi żądania w procesie lub out-of-process, zostanie wyświetlona strona kodowa stanu *niepowodzenia ładowania 500,0-procesowego/wyjściowego* .</span><span class="sxs-lookup"><span data-stu-id="af73c-509">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="af73c-510">W przypadku hostingu w procesie, Jeśli uruchomienie aplikacji przez moduł ASP.NET Core nie powiedzie się, zostanie wyświetlona strona kod stanu *niepowodzenia 500,30* .</span><span class="sxs-lookup"><span data-stu-id="af73c-510">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="af73c-511">W przypadku hostingu poza procesem, jeśli moduł ASP.NET Core nie może uruchomić procesu zaplecza lub proces zaplecza zostanie uruchomiony, ale nie nasłuchuje na skonfigurowanym porcie, zostanie wyświetlona strona kod stanu *niepowodzenia procesu 502,5* .</span><span class="sxs-lookup"><span data-stu-id="af73c-511">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="af73c-512">Aby pominąć tę stronę i przywrócić domyślną stronę kodową stanu 5xx usługi IIS, użyj `disableStartUpErrorPage` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="af73c-512">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="af73c-513">Aby uzyskać więcej informacji o konfigurowaniu niestandardowych komunikatów o błędach, zobacz [Błędy \<httpErrors> http ](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="af73c-513">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="af73c-514">Tworzenie i przekierowywanie dzienników</span><span class="sxs-lookup"><span data-stu-id="af73c-514">Log creation and redirection</span></span>

<span data-ttu-id="af73c-515">Moduł ASP.NET Core przekierowuje dane wyjściowe z konsoli stdout i stderr do dysku, jeśli `stdoutLogEnabled` `stdoutLogFile` atrybuty i `aspNetCore` elementu są ustawione.</span><span class="sxs-lookup"><span data-stu-id="af73c-515">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="af73c-516">Wszystkie foldery w `stdoutLogFile` ścieżce są tworzone przez moduł po utworzeniu pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="af73c-516">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="af73c-517">Pula aplikacji musi mieć dostęp do zapisu w lokalizacji, w której zapisano dzienniki (Użyj `IIS AppPool\<app_pool_name>` do zapewnienia uprawnienia do zapisu).</span><span class="sxs-lookup"><span data-stu-id="af73c-517">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="af73c-518">Dzienniki nie są obracane, chyba że zostanie wykonane odtwarzanie procesów/ponowne uruchomienie.</span><span class="sxs-lookup"><span data-stu-id="af73c-518">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="af73c-519">Ponosisz odpowiedzialność dostawcy usług hostingowych, aby ograniczyć ilość miejsca na dysku zużywanej przez dzienniki.</span><span class="sxs-lookup"><span data-stu-id="af73c-519">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="af73c-520">Użycie dziennika stdout jest zalecane tylko w przypadku rozwiązywania problemów z uruchamianiem aplikacji w usługach IIS lub w przypadku korzystania z [obsługi usług IIS w czasie projektowania w programie Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), a nie podczas debugowania lokalnego i uruchamiania aplikacji przy użyciu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="af73c-520">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="af73c-521">Nie używaj dziennika stdout w celu uzyskania ogólnych celów rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af73c-521">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="af73c-522">Aby rejestrować procedury w aplikacji ASP.NET Core, użyj biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki.</span><span class="sxs-lookup"><span data-stu-id="af73c-522">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="af73c-523">Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.</span><span class="sxs-lookup"><span data-stu-id="af73c-523">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="af73c-524">Sygnatura czasowa i rozszerzenie pliku są dodawane automatycznie podczas tworzenia pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="af73c-524">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="af73c-525">Nazwa pliku dziennika składa się przez dołączenie sygnatury czasowej, identyfikatora procesu i rozszerzenia pliku (*log*) do ostatniego segmentu `stdoutLogFile` ścieżki (zazwyczaj *stdout*), które są rozdzielane znakami podkreślenia.</span><span class="sxs-lookup"><span data-stu-id="af73c-525">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="af73c-526">Jeśli `stdoutLogFile` ścieżka kończy się od *stdout*, dziennik aplikacji o identyfikatorze PID 1934 utworzony w 2/5/2018 o 19:42:32 ma nazwę pliku *stdout_20180205194132_1934. log*.</span><span class="sxs-lookup"><span data-stu-id="af73c-526">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="af73c-527">Jeśli `stdoutLogEnabled` ma wartość false, błędy występujące podczas uruchamiania aplikacji są przechwytywane i emitowane do dziennika zdarzeń do 30 KB.</span><span class="sxs-lookup"><span data-stu-id="af73c-527">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="af73c-528">Po uruchomieniu wszystkie dodatkowe dzienniki są odrzucane.</span><span class="sxs-lookup"><span data-stu-id="af73c-528">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="af73c-529">Poniższy przykładowy `aspNetCore` element konfiguruje rejestrowanie stdout w ścieżce względnej `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="af73c-529">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="af73c-530">Upewnij się, że tożsamość użytkownika puli aplikacji ma uprawnienia do zapisu w podanej ścieżce.</span><span class="sxs-lookup"><span data-stu-id="af73c-530">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="af73c-531">W przypadku publikowania aplikacji dla Azure App Service wdrożenia zestaw SDK sieci Web ustawia `stdoutLogFile` wartość na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="af73c-531">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="af73c-532">`%home`Zmienna środowiskowa jest wstępnie zdefiniowana dla aplikacji hostowanych przez Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="af73c-532">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="af73c-533">Aby uzyskać więcej informacji na temat formatów ścieżki, zobacz [formaty ścieżki plików w systemach Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="af73c-533">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="af73c-534">Udoskonalone dzienniki diagnostyczne</span><span class="sxs-lookup"><span data-stu-id="af73c-534">Enhanced diagnostic logs</span></span>

<span data-ttu-id="af73c-535">Moduł ASP.NET Core można skonfigurować w celu udostępnienia dzienników diagnostyki rozszerzonej.</span><span class="sxs-lookup"><span data-stu-id="af73c-535">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="af73c-536">Dodaj `<handlerSettings>` element do `<aspNetCore>` elementu w *web.config*. Ustawienie w `debugLevel` celu `TRACE` ujawniania większej wierności informacji diagnostycznych:</span><span class="sxs-lookup"><span data-stu-id="af73c-536">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="af73c-537">Foldery w ścieżce przekazanej do `<handlerSetting>` wartości (*dzienniki* w powyższym przykładzie) nie są automatycznie tworzone przez moduł i powinny być wcześniej dostępne we wdrożeniu.</span><span class="sxs-lookup"><span data-stu-id="af73c-537">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="af73c-538">Pula aplikacji musi mieć dostęp do zapisu w lokalizacji, w której zapisano dzienniki (Użyj `IIS AppPool\<app_pool_name>` do zapewnienia uprawnienia do zapisu).</span><span class="sxs-lookup"><span data-stu-id="af73c-538">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="af73c-539">Wartości poziomu debugowania ( `debugLevel` ) mogą zawierać zarówno poziom, jak i lokalizację.</span><span class="sxs-lookup"><span data-stu-id="af73c-539">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="af73c-540">Poziomy (w kolejności od najmniejszej do największej szczegółowości):</span><span class="sxs-lookup"><span data-stu-id="af73c-540">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="af73c-541">BŁĄD</span><span class="sxs-lookup"><span data-stu-id="af73c-541">ERROR</span></span>
* <span data-ttu-id="af73c-542">OSTRZEŻENIE</span><span class="sxs-lookup"><span data-stu-id="af73c-542">WARNING</span></span>
* <span data-ttu-id="af73c-543">INFORMACJE</span><span class="sxs-lookup"><span data-stu-id="af73c-543">INFO</span></span>
* <span data-ttu-id="af73c-544">TRACE</span><span class="sxs-lookup"><span data-stu-id="af73c-544">TRACE</span></span>

<span data-ttu-id="af73c-545">Lokalizacje (wiele lokalizacji jest dozwolonych):</span><span class="sxs-lookup"><span data-stu-id="af73c-545">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="af73c-546">KONSOLI</span><span class="sxs-lookup"><span data-stu-id="af73c-546">CONSOLE</span></span>
* <span data-ttu-id="af73c-547">ELEMENCIE</span><span class="sxs-lookup"><span data-stu-id="af73c-547">EVENTLOG</span></span>
* <span data-ttu-id="af73c-548">PLIK</span><span class="sxs-lookup"><span data-stu-id="af73c-548">FILE</span></span>

<span data-ttu-id="af73c-549">Ustawienia programu obsługi można także zapewnić za pomocą zmiennych środowiskowych:</span><span class="sxs-lookup"><span data-stu-id="af73c-549">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="af73c-550">`ASPNETCORE_MODULE_DEBUG_FILE`: Ścieżka do pliku dziennika debugowania.</span><span class="sxs-lookup"><span data-stu-id="af73c-550">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="af73c-551">(Domyślnie: *aspnetcore-Debug. log*)</span><span class="sxs-lookup"><span data-stu-id="af73c-551">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="af73c-552">`ASPNETCORE_MODULE_DEBUG`: Debugowanie ustawienia poziomu.</span><span class="sxs-lookup"><span data-stu-id="af73c-552">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="af73c-553">**Nie** pozostawiaj włączonej rejestracji debugowania w ramach wdrożenia dłużej niż jest to wymagane, aby rozwiązać problem.</span><span class="sxs-lookup"><span data-stu-id="af73c-553">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="af73c-554">Rozmiar dziennika nie jest ograniczony.</span><span class="sxs-lookup"><span data-stu-id="af73c-554">The size of the log isn't limited.</span></span> <span data-ttu-id="af73c-555">Pozostawienie włączonego dziennika debugowania może spowodować wyczerpanie dostępnego miejsca na dysku i awarię serwera lub usługi App Service.</span><span class="sxs-lookup"><span data-stu-id="af73c-555">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="af73c-556">Zobacz sekcję [Configuration with web.config](#configuration-with-webconfig) , aby zobaczyć przykład `aspNetCore` elementu w pliku *web.config* .</span><span class="sxs-lookup"><span data-stu-id="af73c-556">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="af73c-557">Konfiguracja serwera proxy używa protokołu HTTP i tokenu parowania</span><span class="sxs-lookup"><span data-stu-id="af73c-557">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="af73c-558">*Dotyczy tylko hostingu poza procesem.*</span><span class="sxs-lookup"><span data-stu-id="af73c-558">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="af73c-559">Serwer proxy utworzony między modułem ASP.NET Core a Kestrel używa protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="af73c-559">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="af73c-560">Nie ma ryzyka podsłuchiwanie ruchu między modułem i Kestrel z lokalizacji poza serwerem.</span><span class="sxs-lookup"><span data-stu-id="af73c-560">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="af73c-561">Token parowania jest używany w celu zagwarantowania, że żądania odbierane przez Kestrel zostały przekazane przez usługi IIS i nie pochodzą z innego źródła.</span><span class="sxs-lookup"><span data-stu-id="af73c-561">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="af73c-562">Token parowania jest tworzony i ustawiany jako zmienna środowiskowa ( `ASPNETCORE_TOKEN` ) przez moduł.</span><span class="sxs-lookup"><span data-stu-id="af73c-562">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="af73c-563">Token parowania jest również ustawiany w nagłówku ( `MS-ASPNETCORE-TOKEN` ) na każdym żądaniu z serwerem proxy.</span><span class="sxs-lookup"><span data-stu-id="af73c-563">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="af73c-564">Oprogramowanie pośredniczące usług IIS sprawdza każde odebrane żądanie, aby potwierdzić, że wartość nagłówka tokenu parowania jest zgodna z wartością zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="af73c-564">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="af73c-565">Jeśli wartości tokenu są niezgodne, żądanie zostanie zarejestrowane i odrzucone.</span><span class="sxs-lookup"><span data-stu-id="af73c-565">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="af73c-566">Zmienna środowiskowa tokena parowania i ruch między modułem i Kestrel nie są dostępne z lokalizacji poza serwerem.</span><span class="sxs-lookup"><span data-stu-id="af73c-566">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="af73c-567">Bez znajomości wartości tokenu parowania osoba atakująca nie może przesłać żądań, które pomijają Ewidencjonowanie oprogramowania pośredniczącego usług IIS.</span><span class="sxs-lookup"><span data-stu-id="af73c-567">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="af73c-568">Moduł ASP.NET Core z konfiguracją udostępnioną usług IIS</span><span class="sxs-lookup"><span data-stu-id="af73c-568">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="af73c-569">Instalator modułu ASP.NET Core jest uruchamiany z uprawnieniami konta **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="af73c-569">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="af73c-570">Ponieważ konto systemu lokalnego nie ma uprawnień do modyfikowania dla ścieżki udziału używanej przez udostępnioną konfigurację usług IIS, Instalator zgłasza błąd odmowy dostępu podczas próby skonfigurowania ustawień modułu w pliku *applicationHost.config* w udziale.</span><span class="sxs-lookup"><span data-stu-id="af73c-570">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="af73c-571">W przypadku korzystania z konfiguracji udostępnionej przez usługi IIS na tym samym komputerze, na którym znajduje się instalacja usług IIS, uruchom Instalatora pakietu ASP.NET Core hostowania z `OPT_NO_SHARED_CONFIG_CHECK` parametrem ustawionym na `1` :</span><span class="sxs-lookup"><span data-stu-id="af73c-571">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="af73c-572">Jeśli ścieżka do konfiguracji udostępnionej nie znajduje się na tym samym komputerze, na którym znajduje się instalacja usług IIS, wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="af73c-572">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="af73c-573">Wyłącz konfigurację udostępnioną usług IIS.</span><span class="sxs-lookup"><span data-stu-id="af73c-573">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="af73c-574">Uruchom instalatora.</span><span class="sxs-lookup"><span data-stu-id="af73c-574">Run the installer.</span></span>
1. <span data-ttu-id="af73c-575">Wyeksportuj zaktualizowany plik *applicationHost.config* do udziału.</span><span class="sxs-lookup"><span data-stu-id="af73c-575">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="af73c-576">Włącz ponownie konfigurację udostępnioną usług IIS.</span><span class="sxs-lookup"><span data-stu-id="af73c-576">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="af73c-577">Dzienniki instalacji pakietu i pakietów hostingu</span><span class="sxs-lookup"><span data-stu-id="af73c-577">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="af73c-578">Aby określić wersję zainstalowanego modułu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="af73c-578">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="af73c-579">W systemie hostingu przejdź do *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="af73c-579">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="af73c-580">Znajdź plik *aspnetcore.dll* .</span><span class="sxs-lookup"><span data-stu-id="af73c-580">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="af73c-581">Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Właściwości** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="af73c-581">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="af73c-582">Wybierz kartę **szczegóły** . **Wersja pliku** i **Wersja produktu** reprezentują zainstalowaną wersję modułu.</span><span class="sxs-lookup"><span data-stu-id="af73c-582">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="af73c-583">Dzienniki Instalatora pakietu hostingu dla modułu znajdują się pod adresem *C: \\ Użytkownicy \\ % username% \\ AppData \\ Local \\ temp*. Plik ma nazwę *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="af73c-583">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="af73c-584">Lokalizacje pliku modułu, schematu i konfiguracji</span><span class="sxs-lookup"><span data-stu-id="af73c-584">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="af73c-585">Moduł</span><span class="sxs-lookup"><span data-stu-id="af73c-585">Module</span></span>

<span data-ttu-id="af73c-586">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="af73c-586">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="af73c-587">% windir% \System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="af73c-587">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="af73c-588">% windir% \SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="af73c-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="af73c-589">Module\V2\aspnetcorev2.dll rdzeń%ProgramFiles%\IIS\Asp.Net</span><span class="sxs-lookup"><span data-stu-id="af73c-589">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="af73c-590">% ProgramFiles (x86)% \ IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="af73c-590">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="af73c-591">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="af73c-591">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="af73c-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="af73c-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="af73c-593">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="af73c-593">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="af73c-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="af73c-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="af73c-595">% ProgramFiles (x86)% \ Express\Asp.Net Module\V2\aspnetcorev2.dll Core usług IIS</span><span class="sxs-lookup"><span data-stu-id="af73c-595">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="af73c-596">Schemat</span><span class="sxs-lookup"><span data-stu-id="af73c-596">Schema</span></span>

<span data-ttu-id="af73c-597">**IIS**</span><span class="sxs-lookup"><span data-stu-id="af73c-597">**IIS**</span></span>

* <span data-ttu-id="af73c-598">% windir% \System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="af73c-598">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="af73c-599">% windir% \System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="af73c-599">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="af73c-600">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="af73c-600">**IIS Express**</span></span>

* <span data-ttu-id="af73c-601">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="af73c-601">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="af73c-602">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="af73c-602">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="af73c-603">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="af73c-603">Configuration</span></span>

<span data-ttu-id="af73c-604">**IIS**</span><span class="sxs-lookup"><span data-stu-id="af73c-604">**IIS**</span></span>

* <span data-ttu-id="af73c-605">% windir% \System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="af73c-605">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="af73c-606">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="af73c-606">**IIS Express**</span></span>

* <span data-ttu-id="af73c-607">Visual Studio: {Aplikacja główna} \\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="af73c-607">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="af73c-608">*iisexpress.exe* INTERFEJS WIERSZA POLECENIA:% USERPROFILE% \Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="af73c-608">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="af73c-609">Pliki można znaleźć, wyszukując *aspnetcore* w pliku *applicationHost.config* .</span><span class="sxs-lookup"><span data-stu-id="af73c-609">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="af73c-610">Moduł ASP.NET Core jest natywnym modułem usług IIS, który jest podłączany do potoku usług IIS do przesyłania dalej żądań sieci Web do zaplecza ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af73c-610">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="af73c-611">Obsługiwane wersje systemu Windows:</span><span class="sxs-lookup"><span data-stu-id="af73c-611">Supported Windows versions:</span></span>

* <span data-ttu-id="af73c-612">System Windows 7 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="af73c-612">Windows 7 or later</span></span>
* <span data-ttu-id="af73c-613">Windows Server 2008 R2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="af73c-613">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="af73c-614">Moduł działa tylko z Kestrel.</span><span class="sxs-lookup"><span data-stu-id="af73c-614">The module only works with Kestrel.</span></span> <span data-ttu-id="af73c-615">Moduł jest niezgodny z [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="af73c-615">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="af73c-616">Ponieważ ASP.NET Core aplikacje działają w procesie innym niż proces roboczy usług IIS, moduł obsługuje również zarządzanie procesami.</span><span class="sxs-lookup"><span data-stu-id="af73c-616">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="af73c-617">Moduł uruchamia proces dla aplikacji ASP.NET Core, gdy pierwsze żądanie zostanie odebrane i ponownie uruchomiony, jeśli wystąpi awaria.</span><span class="sxs-lookup"><span data-stu-id="af73c-617">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="af73c-618">Jest to zasadniczo takie samo zachowanie jak w przypadku aplikacji ASP.NET 4. x, które działają w procesie w usługach IIS, które są zarządzane przez [usługę aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="af73c-618">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="af73c-619">Na poniższym diagramie przedstawiono relację między usługami IIS, modułem ASP.NET Core i aplikacją:</span><span class="sxs-lookup"><span data-stu-id="af73c-619">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Moduł ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="af73c-621">Żądania docierają z sieci Web do sterownika HTTP.sys trybu jądra.</span><span class="sxs-lookup"><span data-stu-id="af73c-621">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="af73c-622">Sterownik kieruje żądania do usług IIS na skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="af73c-622">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="af73c-623">Moduł przekazuje żądania do Kestrel na losowo wybranym porcie dla aplikacji, która nie jest portem 80 lub 443.</span><span class="sxs-lookup"><span data-stu-id="af73c-623">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="af73c-624">Moduł określa port za pośrednictwem zmiennej środowiskowej podczas uruchamiania, a [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) konfiguruje serwer do nasłuchiwania `http://localhost:{port}` .</span><span class="sxs-lookup"><span data-stu-id="af73c-624">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="af73c-625">Dodatkowe sprawdzenia są wykonywane, a żądania, które nie pochodzą z modułu, są odrzucane.</span><span class="sxs-lookup"><span data-stu-id="af73c-625">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="af73c-626">Moduł nie obsługuje przekazywania HTTPS, dlatego żądania są przekazywane przez protokół HTTP nawet wtedy, gdy są odbierane przez usługę IIS przez protokół HTTPS.</span><span class="sxs-lookup"><span data-stu-id="af73c-626">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="af73c-627">Po podaniu przez Kestrel żądania z modułu żądanie jest wypychane do potoku ASP.NET Core pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="af73c-627">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="af73c-628">Potok oprogramowania pośredniczącego obsługuje żądanie i przekazuje go jako `HttpContext` wystąpienie do logiki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af73c-628">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="af73c-629">Oprogramowanie pośredniczące dodane przez integrację usług IIS aktualizuje schemat, zdalny adres IP i pathbase, aby można było przesłać żądanie do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="af73c-629">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="af73c-630">Odpowiedź aplikacji jest przesyłana z powrotem do usług IIS, która wypycha ją z powrotem do klienta HTTP, który zainicjował żądanie.</span><span class="sxs-lookup"><span data-stu-id="af73c-630">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="af73c-631">Wiele modułów macierzystych, takich jak uwierzytelnianie systemu Windows, pozostaje aktywnych.</span><span class="sxs-lookup"><span data-stu-id="af73c-631">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="af73c-632">Aby dowiedzieć się więcej na temat modułów usług IIS aktywnych przy użyciu modułu ASP.NET Core, zobacz <xref:host-and-deploy/iis/modules> .</span><span class="sxs-lookup"><span data-stu-id="af73c-632">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="af73c-633">Moduł ASP.NET Core może również:</span><span class="sxs-lookup"><span data-stu-id="af73c-633">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="af73c-634">Ustaw zmienne środowiskowe dla procesu roboczego.</span><span class="sxs-lookup"><span data-stu-id="af73c-634">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="af73c-635">Rejestruj dane wyjściowe stdout do magazynu plików w celu rozwiązywania problemów z uruchamianiem.</span><span class="sxs-lookup"><span data-stu-id="af73c-635">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="af73c-636">Przekazuj tokeny uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="af73c-636">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="af73c-637">Jak zainstalować moduł ASP.NET Core i korzystać z niego</span><span class="sxs-lookup"><span data-stu-id="af73c-637">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="af73c-638">Aby uzyskać instrukcje dotyczące sposobu instalowania modułu ASP.NET Core, zobacz [installing the .NET Core hosting](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="af73c-638">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="af73c-639">Konfiguracja z web.config</span><span class="sxs-lookup"><span data-stu-id="af73c-639">Configuration with web.config</span></span>

<span data-ttu-id="af73c-640">Moduł ASP.NET Core jest skonfigurowany przy użyciu `aspNetCore` sekcji `system.webServer` węzła w pliku *web.config* witryny.</span><span class="sxs-lookup"><span data-stu-id="af73c-640">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="af73c-641">Następujący *web.config* plik jest publikowany dla [wdrożenia zależnego od platformy](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) i konfiguruje moduł ASP.NET Core do obsługi żądań lokacji:</span><span class="sxs-lookup"><span data-stu-id="af73c-641">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="af73c-642">Następujące *web.config* są publikowane dla [wdrożenia samodzielnego](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="af73c-642">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="af73c-643">Gdy aplikacja zostanie wdrożona do [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` ścieżka jest ustawiona na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="af73c-643">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="af73c-644">Ścieżka zapisuje dzienniki stdout do folderu *LogFiles* , który jest lokalizacją automatycznie utworzoną przez usługę.</span><span class="sxs-lookup"><span data-stu-id="af73c-644">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="af73c-645">Aby uzyskać informacje na temat konfiguracji aplikacji podrzędnych usług IIS, zobacz <xref:host-and-deploy/iis/index#sub-applications> .</span><span class="sxs-lookup"><span data-stu-id="af73c-645">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="af73c-646">Atrybuty elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="af73c-646">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="af73c-647">Atrybut</span><span class="sxs-lookup"><span data-stu-id="af73c-647">Attribute</span></span> | <span data-ttu-id="af73c-648">Opis</span><span class="sxs-lookup"><span data-stu-id="af73c-648">Description</span></span> | <span data-ttu-id="af73c-649">Domyślne</span><span class="sxs-lookup"><span data-stu-id="af73c-649">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="af73c-650">Opcjonalny atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="af73c-650">Optional string attribute.</span></span></p><p><span data-ttu-id="af73c-651">Argumenty do pliku wykonywalnego określonego w **processPath**.</span><span class="sxs-lookup"><span data-stu-id="af73c-651">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="af73c-652">Opcjonalny atrybut Boolean.</span><span class="sxs-lookup"><span data-stu-id="af73c-652">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="af73c-653">W przypadku wartości true strona **błędu 502,5 procesu** jest pomijana, a strona kodu stanu 502 skonfigurowana w *web.config* ma pierwszeństwo.</span><span class="sxs-lookup"><span data-stu-id="af73c-653">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="af73c-654">Opcjonalny atrybut Boolean.</span><span class="sxs-lookup"><span data-stu-id="af73c-654">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="af73c-655">Jeśli wartość jest równa true, token jest przekazywany do procesu podrzędnego, który nasłuchuje na% ASPNETCORE_PORT% jako nagłówek "MS-ASPNETCORE-WINAUTHTOKEN" na żądanie.</span><span class="sxs-lookup"><span data-stu-id="af73c-655">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="af73c-656">Jest odpowiedzialny za ten proces, aby wywołać metodę CloseHandle na tym tokenie na żądanie.</span><span class="sxs-lookup"><span data-stu-id="af73c-656">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="af73c-657">Opcjonalny atrybut Integer.</span><span class="sxs-lookup"><span data-stu-id="af73c-657">Optional integer attribute.</span></span></p><p><span data-ttu-id="af73c-658">Określa liczbę wystąpień procesu określonego w ustawieniu **processPath** , które może być przypadające na aplikację.</span><span class="sxs-lookup"><span data-stu-id="af73c-658">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="af73c-659">Ustawienie `processesPerApplication` jest niezalecane.</span><span class="sxs-lookup"><span data-stu-id="af73c-659">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="af73c-660">Ten atrybut zostanie usunięty w przyszłych wydaniach.</span><span class="sxs-lookup"><span data-stu-id="af73c-660">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="af73c-661">Wartooć`1`</span><span class="sxs-lookup"><span data-stu-id="af73c-661">Default: `1`</span></span><br><span data-ttu-id="af73c-662">Długości`1`</span><span class="sxs-lookup"><span data-stu-id="af73c-662">Min: `1`</span></span><br><span data-ttu-id="af73c-663">Maksymalny`100`</span><span class="sxs-lookup"><span data-stu-id="af73c-663">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="af73c-664">Wymagany atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="af73c-664">Required string attribute.</span></span></p><p><span data-ttu-id="af73c-665">Ścieżka do pliku wykonywalnego, który uruchamia proces nasłuchiwanie żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="af73c-665">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="af73c-666">Obsługiwane są ścieżki względne.</span><span class="sxs-lookup"><span data-stu-id="af73c-666">Relative paths are supported.</span></span> <span data-ttu-id="af73c-667">Jeśli ścieżka zaczyna się od `.` , ścieżka jest uznawana za względną względem katalogu głównego witryny.</span><span class="sxs-lookup"><span data-stu-id="af73c-667">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="af73c-668">Opcjonalny atrybut Integer.</span><span class="sxs-lookup"><span data-stu-id="af73c-668">Optional integer attribute.</span></span></p><p><span data-ttu-id="af73c-669">Określa, ile razy proces określony w **processPath** może ulec awarii na minutę.</span><span class="sxs-lookup"><span data-stu-id="af73c-669">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="af73c-670">W przypadku przekroczenia tego limitu moduł przestaje uruchomić proces przez pozostałą część minuty.</span><span class="sxs-lookup"><span data-stu-id="af73c-670">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="af73c-671">Wartooć`10`</span><span class="sxs-lookup"><span data-stu-id="af73c-671">Default: `10`</span></span><br><span data-ttu-id="af73c-672">Długości`0`</span><span class="sxs-lookup"><span data-stu-id="af73c-672">Min: `0`</span></span><br><span data-ttu-id="af73c-673">Maksymalny`100`</span><span class="sxs-lookup"><span data-stu-id="af73c-673">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="af73c-674">Opcjonalny atrybut TimeSpan.</span><span class="sxs-lookup"><span data-stu-id="af73c-674">Optional timespan attribute.</span></span></p><p><span data-ttu-id="af73c-675">Określa czas, przez który moduł ASP.NET Core czeka na odpowiedź z procesu nasłuchiwania na% ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="af73c-675">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="af73c-676">W wersjach modułu ASP.NET Core, który został dostarczony z wersją ASP.NET Core 2,1 lub nowszą, wartość `requestTimeout` jest określona w godzinach, minutach i sekundach.</span><span class="sxs-lookup"><span data-stu-id="af73c-676">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="af73c-677">Wartooć`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="af73c-677">Default: `00:02:00`</span></span><br><span data-ttu-id="af73c-678">Długości`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="af73c-678">Min: `00:00:00`</span></span><br><span data-ttu-id="af73c-679">Maksymalny`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="af73c-679">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="af73c-680">Opcjonalny atrybut Integer.</span><span class="sxs-lookup"><span data-stu-id="af73c-680">Optional integer attribute.</span></span></p><p><span data-ttu-id="af73c-681">Czas w sekundach, przez który moduł czeka, aż plik wykonywalny zostanie bezpiecznie zamknięty po wykryciu pliku *app_offline.htm* .</span><span class="sxs-lookup"><span data-stu-id="af73c-681">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="af73c-682">Wartooć`10`</span><span class="sxs-lookup"><span data-stu-id="af73c-682">Default: `10`</span></span><br><span data-ttu-id="af73c-683">Długości`0`</span><span class="sxs-lookup"><span data-stu-id="af73c-683">Min: `0`</span></span><br><span data-ttu-id="af73c-684">Maksymalny`600`</span><span class="sxs-lookup"><span data-stu-id="af73c-684">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="af73c-685">Opcjonalny atrybut Integer.</span><span class="sxs-lookup"><span data-stu-id="af73c-685">Optional integer attribute.</span></span></p><p><span data-ttu-id="af73c-686">Czas w sekundach, przez który moduł czeka, aż plik wykonywalny uruchomi proces nasłuchujący na porcie.</span><span class="sxs-lookup"><span data-stu-id="af73c-686">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="af73c-687">Jeśli ten limit czasu zostanie przekroczony, moduł zakasuje proces.</span><span class="sxs-lookup"><span data-stu-id="af73c-687">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="af73c-688">Moduł podejmuje próbę ponownego uruchomienia procesu, gdy odbierze nowe żądanie i kontynuuje ponowne uruchomienie procesu na kolejnych żądaniach przychodzących, chyba że aplikacja nie będzie mogła uruchomić **rapidFailsPerMinute** liczbę razy w ostatniej minucie.</span><span class="sxs-lookup"><span data-stu-id="af73c-688">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="af73c-689">Wartość 0 (zero) **nie** jest uważana za nieskończony limit czasu.</span><span class="sxs-lookup"><span data-stu-id="af73c-689">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="af73c-690">Wartooć`120`</span><span class="sxs-lookup"><span data-stu-id="af73c-690">Default: `120`</span></span><br><span data-ttu-id="af73c-691">Długości`0`</span><span class="sxs-lookup"><span data-stu-id="af73c-691">Min: `0`</span></span><br><span data-ttu-id="af73c-692">Maksymalny`3600`</span><span class="sxs-lookup"><span data-stu-id="af73c-692">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="af73c-693">Opcjonalny atrybut Boolean.</span><span class="sxs-lookup"><span data-stu-id="af73c-693">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="af73c-694">Jeśli wartość jest równa true, **stdout** i **stderr** dla procesu określonego w **processPath** są przekierowywane do pliku określonego w **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="af73c-694">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="af73c-695">Opcjonalny atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="af73c-695">Optional string attribute.</span></span></p><p><span data-ttu-id="af73c-696">Określa względną lub bezwzględną ścieżkę do pliku, dla którego jest rejestrowany **stdout** i **stderr** z procesu określonego w **processPath** .</span><span class="sxs-lookup"><span data-stu-id="af73c-696">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="af73c-697">Ścieżki względne są względne względem katalogu głównego witryny.</span><span class="sxs-lookup"><span data-stu-id="af73c-697">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="af73c-698">Każda ścieżka rozpoczynająca `.` się od jest określana względem katalogu głównego witryny, a wszystkie inne ścieżki są traktowane jako ścieżki bezwzględne.</span><span class="sxs-lookup"><span data-stu-id="af73c-698">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="af73c-699">Wszystkie foldery podane w ścieżce muszą istnieć, aby moduł mógł utworzyć plik dziennika.</span><span class="sxs-lookup"><span data-stu-id="af73c-699">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="af73c-700">Przy użyciu ograniczników podkreślenia, sygnatury czasowej, identyfikatora procesu i rozszerzenia pliku (*log*) są dodawane do ostatniego segmentu ścieżki **stdoutLogFile** .</span><span class="sxs-lookup"><span data-stu-id="af73c-700">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="af73c-701">Jeśli `.\logs\stdout` jest podana jako wartość, przykładowy dziennik stdout jest zapisywany jako *stdout_20180205194132_1934. log* w folderze *Logs* , gdy jest zapisywany na 2/5/2018 o godzinie 19:41:32 przy użyciu identyfikatora procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="af73c-701">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="af73c-702">Ustawianie zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="af73c-702">Setting environment variables</span></span>

<span data-ttu-id="af73c-703">Zmienne środowiskowe można określić dla procesu w `processPath` atrybucie.</span><span class="sxs-lookup"><span data-stu-id="af73c-703">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="af73c-704">Określ zmienną środowiskową z `<environmentVariable>` elementem podrzędnym `<environmentVariables>` elementu kolekcji.</span><span class="sxs-lookup"><span data-stu-id="af73c-704">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="af73c-705">Zmienne środowiskowe ustawione w tej sekcji powodują konflikt z systemowymi zmiennymi środowiskowymi ustawionymi z tą samą nazwą.</span><span class="sxs-lookup"><span data-stu-id="af73c-705">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="af73c-706">Jeśli zmienna środowiskowa jest ustawiona zarówno w pliku *web.config* , jak i na poziomie systemu w systemie Windows, wartość z pliku *web.config* zostanie dołączona do systemowej wartości zmiennej środowiskowej (na przykład `ASPNETCORE_ENVIRONMENT: Development;Development` ), co uniemożliwia uruchomienie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af73c-706">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="af73c-707">W poniższym przykładzie są ustawiane dwie zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="af73c-707">The following example sets two environment variables.</span></span> <span data-ttu-id="af73c-708">`ASPNETCORE_ENVIRONMENT`konfiguruje środowisko aplikacji do programu `Development` .</span><span class="sxs-lookup"><span data-stu-id="af73c-708">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="af73c-709">Deweloper może tymczasowo ustawić tę wartość w pliku *web.config* w celu wymuszenia załadowania [strony wyjątku dewelopera](xref:fundamentals/error-handling) podczas debugowania wyjątku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af73c-709">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="af73c-710">`CONFIG_DIR`to przykład zmiennej środowiskowej zdefiniowanej przez użytkownika, w której deweloper ma napisać kod, który odczytuje wartość przy uruchamianiu, aby utworzyć ścieżkę do ładowania pliku konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af73c-710">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="af73c-711">Dla `ASPNETCORE_ENVIRONMENT` zmiennej środowiskowej należy ustawić tylko `Development` serwery przejściowe i testowe, które nie są dostępne dla niezaufanych sieci, takich jak Internet.</span><span class="sxs-lookup"><span data-stu-id="af73c-711">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="af73c-712">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="af73c-712">app_offline.htm</span></span>

<span data-ttu-id="af73c-713">Jeśli plik o nazwie *app_offline.htm* zostanie wykryty w katalogu głównym aplikacji, moduł ASP.NET Core próbuje bezpiecznie zamknąć aplikację i zatrzymać przetwarzanie żądań przychodzących.</span><span class="sxs-lookup"><span data-stu-id="af73c-713">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="af73c-714">Jeśli aplikacja nadal działa po upływie liczby sekund zdefiniowanej w programie `shutdownTimeLimit` , moduł ASP.NET Core kasuje uruchomiony proces.</span><span class="sxs-lookup"><span data-stu-id="af73c-714">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="af73c-715">Gdy plik *app_offline.htm* jest obecny, moduł ASP.NET Core reaguje na żądania, wysyłając z powrotem zawartość pliku *app_offline.htm* .</span><span class="sxs-lookup"><span data-stu-id="af73c-715">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="af73c-716">Po usunięciu pliku *app_offline.htm* następnym żądaniu zostanie uruchomiona aplikacja.</span><span class="sxs-lookup"><span data-stu-id="af73c-716">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="af73c-717">Strona błędu uruchamiania</span><span class="sxs-lookup"><span data-stu-id="af73c-717">Start-up error page</span></span>

<span data-ttu-id="af73c-718">Jeśli moduł ASP.NET Core nie może uruchomić procesu zaplecza lub proces zaplecza zostanie uruchomiony, ale nie nasłuchuje na skonfigurowanym porcie, zostanie wyświetlona strona kod stanu *niepowodzenia procesu 502,5* .</span><span class="sxs-lookup"><span data-stu-id="af73c-718">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="af73c-719">Aby pominąć tę stronę i przywrócić domyślną stronę kodową stanu 502 usług IIS, użyj `disableStartUpErrorPage` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="af73c-719">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="af73c-720">Aby uzyskać więcej informacji o konfigurowaniu niestandardowych komunikatów o błędach, zobacz [Błędy \<httpErrors> http ](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="af73c-720">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![Strona kodowa stanu niepowodzenia procesów 502,5](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="af73c-722">Tworzenie i przekierowywanie dzienników</span><span class="sxs-lookup"><span data-stu-id="af73c-722">Log creation and redirection</span></span>

<span data-ttu-id="af73c-723">Moduł ASP.NET Core przekierowuje dane wyjściowe z konsoli stdout i stderr do dysku, jeśli `stdoutLogEnabled` `stdoutLogFile` atrybuty i `aspNetCore` elementu są ustawione.</span><span class="sxs-lookup"><span data-stu-id="af73c-723">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="af73c-724">Wszystkie foldery w `stdoutLogFile` ścieżce są tworzone przez moduł po utworzeniu pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="af73c-724">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="af73c-725">Pula aplikacji musi mieć dostęp do zapisu w lokalizacji, w której zapisano dzienniki (Użyj `IIS AppPool\<app_pool_name>` do zapewnienia uprawnienia do zapisu).</span><span class="sxs-lookup"><span data-stu-id="af73c-725">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="af73c-726">Dzienniki nie są obracane, chyba że zostanie wykonane odtwarzanie procesów/ponowne uruchomienie.</span><span class="sxs-lookup"><span data-stu-id="af73c-726">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="af73c-727">Ponosisz odpowiedzialność dostawcy usług hostingowych, aby ograniczyć ilość miejsca na dysku zużywanej przez dzienniki.</span><span class="sxs-lookup"><span data-stu-id="af73c-727">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="af73c-728">Użycie dziennika stdout jest zalecane tylko w przypadku rozwiązywania problemów z uruchamianiem aplikacji w usługach IIS lub w przypadku korzystania z [obsługi usług IIS w czasie projektowania w programie Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), a nie podczas debugowania lokalnego i uruchamiania aplikacji przy użyciu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="af73c-728">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="af73c-729">Nie używaj dziennika stdout w celu uzyskania ogólnych celów rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="af73c-729">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="af73c-730">Aby rejestrować procedury w aplikacji ASP.NET Core, użyj biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki.</span><span class="sxs-lookup"><span data-stu-id="af73c-730">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="af73c-731">Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.</span><span class="sxs-lookup"><span data-stu-id="af73c-731">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="af73c-732">Sygnatura czasowa i rozszerzenie pliku są dodawane automatycznie podczas tworzenia pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="af73c-732">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="af73c-733">Nazwa pliku dziennika składa się przez dołączenie sygnatury czasowej, identyfikatora procesu i rozszerzenia pliku (*log*) do ostatniego segmentu `stdoutLogFile` ścieżki (zazwyczaj *stdout*), które są rozdzielane znakami podkreślenia.</span><span class="sxs-lookup"><span data-stu-id="af73c-733">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="af73c-734">Jeśli `stdoutLogFile` ścieżka kończy się od *stdout*, dziennik aplikacji o identyfikatorze PID 1934 utworzony w 2/5/2018 o 19:42:32 ma nazwę pliku *stdout_20180205194132_1934. log*.</span><span class="sxs-lookup"><span data-stu-id="af73c-734">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="af73c-735">Poniższy przykładowy `aspNetCore` element konfiguruje rejestrowanie stdout w ścieżce względnej `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="af73c-735">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="af73c-736">Upewnij się, że tożsamość użytkownika puli aplikacji ma uprawnienia do zapisu w podanej ścieżce.</span><span class="sxs-lookup"><span data-stu-id="af73c-736">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="af73c-737">W przypadku publikowania aplikacji dla Azure App Service wdrożenia zestaw SDK sieci Web ustawia `stdoutLogFile` wartość na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="af73c-737">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="af73c-738">`%home`Zmienna środowiskowa jest wstępnie zdefiniowana dla aplikacji hostowanych przez Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="af73c-738">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="af73c-739">Aby utworzyć reguły filtru rejestrowania, zobacz sekcje [Konfiguracja](xref:fundamentals/logging/index#log-filtering) i [filtrowanie dzienników](xref:fundamentals/logging/index#log-filtering) w dokumentacji rejestrowania ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af73c-739">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="af73c-740">Aby uzyskać więcej informacji na temat formatów ścieżki, zobacz [formaty ścieżki plików w systemach Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="af73c-740">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="af73c-741">Konfiguracja serwera proxy używa protokołu HTTP i tokenu parowania</span><span class="sxs-lookup"><span data-stu-id="af73c-741">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="af73c-742">Serwer proxy utworzony między modułem ASP.NET Core a Kestrel używa protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="af73c-742">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="af73c-743">Nie ma ryzyka podsłuchiwanie ruchu między modułem i Kestrel z lokalizacji poza serwerem.</span><span class="sxs-lookup"><span data-stu-id="af73c-743">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="af73c-744">Token parowania jest używany w celu zagwarantowania, że żądania odbierane przez Kestrel zostały przekazane przez usługi IIS i nie pochodzą z innego źródła.</span><span class="sxs-lookup"><span data-stu-id="af73c-744">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="af73c-745">Token parowania jest tworzony i ustawiany jako zmienna środowiskowa ( `ASPNETCORE_TOKEN` ) przez moduł.</span><span class="sxs-lookup"><span data-stu-id="af73c-745">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="af73c-746">Token parowania jest również ustawiany w nagłówku ( `MS-ASPNETCORE-TOKEN` ) na każdym żądaniu z serwerem proxy.</span><span class="sxs-lookup"><span data-stu-id="af73c-746">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="af73c-747">Oprogramowanie pośredniczące usług IIS sprawdza każde odebrane żądanie, aby potwierdzić, że wartość nagłówka tokenu parowania jest zgodna z wartością zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="af73c-747">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="af73c-748">Jeśli wartości tokenu są niezgodne, żądanie zostanie zarejestrowane i odrzucone.</span><span class="sxs-lookup"><span data-stu-id="af73c-748">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="af73c-749">Zmienna środowiskowa tokena parowania i ruch między modułem i Kestrel nie są dostępne z lokalizacji poza serwerem.</span><span class="sxs-lookup"><span data-stu-id="af73c-749">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="af73c-750">Bez znajomości wartości tokenu parowania osoba atakująca nie może przesłać żądań, które pomijają Ewidencjonowanie oprogramowania pośredniczącego usług IIS.</span><span class="sxs-lookup"><span data-stu-id="af73c-750">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="af73c-751">Moduł ASP.NET Core z konfiguracją udostępnioną usług IIS</span><span class="sxs-lookup"><span data-stu-id="af73c-751">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="af73c-752">Instalator modułu ASP.NET Core jest uruchamiany z uprawnieniami konta **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="af73c-752">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="af73c-753">Ponieważ konto systemu lokalnego nie ma uprawnień do modyfikowania dla ścieżki udziału używanej przez udostępnioną konfigurację usług IIS, Instalator zgłasza błąd odmowy dostępu podczas próby skonfigurowania ustawień modułu w pliku *applicationHost.config* w udziale.</span><span class="sxs-lookup"><span data-stu-id="af73c-753">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="af73c-754">W przypadku korzystania z konfiguracji udostępnionej usług IIS wykonaj następujące kroki:</span><span class="sxs-lookup"><span data-stu-id="af73c-754">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="af73c-755">Wyłącz konfigurację udostępnioną usług IIS.</span><span class="sxs-lookup"><span data-stu-id="af73c-755">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="af73c-756">Uruchom instalatora.</span><span class="sxs-lookup"><span data-stu-id="af73c-756">Run the installer.</span></span>
1. <span data-ttu-id="af73c-757">Wyeksportuj zaktualizowany plik *applicationHost.config* do udziału.</span><span class="sxs-lookup"><span data-stu-id="af73c-757">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="af73c-758">Włącz ponownie konfigurację udostępnioną usług IIS.</span><span class="sxs-lookup"><span data-stu-id="af73c-758">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="af73c-759">Dzienniki instalacji pakietu i pakietów hostingu</span><span class="sxs-lookup"><span data-stu-id="af73c-759">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="af73c-760">Aby określić wersję zainstalowanego modułu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="af73c-760">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="af73c-761">W systemie hostingu przejdź do *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="af73c-761">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="af73c-762">Znajdź plik *aspnetcore.dll* .</span><span class="sxs-lookup"><span data-stu-id="af73c-762">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="af73c-763">Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Właściwości** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="af73c-763">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="af73c-764">Wybierz kartę **szczegóły** . **Wersja pliku** i **Wersja produktu** reprezentują zainstalowaną wersję modułu.</span><span class="sxs-lookup"><span data-stu-id="af73c-764">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="af73c-765">Dzienniki Instalatora pakietu hostingu dla modułu znajdują się pod adresem *C: \\ Użytkownicy \\ % username% \\ AppData \\ Local \\ temp*. Plik ma nazwę *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="af73c-765">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="af73c-766">Lokalizacje pliku modułu, schematu i konfiguracji</span><span class="sxs-lookup"><span data-stu-id="af73c-766">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="af73c-767">Moduł</span><span class="sxs-lookup"><span data-stu-id="af73c-767">Module</span></span>

<span data-ttu-id="af73c-768">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="af73c-768">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="af73c-769">% windir% \System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="af73c-769">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="af73c-770">% windir% \SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="af73c-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="af73c-771">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="af73c-771">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="af73c-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="af73c-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="af73c-773">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="af73c-773">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="af73c-774">Schemat</span><span class="sxs-lookup"><span data-stu-id="af73c-774">Schema</span></span>

<span data-ttu-id="af73c-775">**IIS**</span><span class="sxs-lookup"><span data-stu-id="af73c-775">**IIS**</span></span>

* <span data-ttu-id="af73c-776">% windir% \System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="af73c-776">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="af73c-777">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="af73c-777">**IIS Express**</span></span>

* <span data-ttu-id="af73c-778">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="af73c-778">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="af73c-779">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="af73c-779">Configuration</span></span>

<span data-ttu-id="af73c-780">**IIS**</span><span class="sxs-lookup"><span data-stu-id="af73c-780">**IIS**</span></span>

* <span data-ttu-id="af73c-781">% windir% \System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="af73c-781">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="af73c-782">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="af73c-782">**IIS Express**</span></span>

* <span data-ttu-id="af73c-783">Visual Studio: {Aplikacja główna} \\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="af73c-783">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="af73c-784">*iisexpress.exe* INTERFEJS WIERSZA POLECENIA:% USERPROFILE% \Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="af73c-784">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="af73c-785">Pliki można znaleźć, wyszukując *aspnetcore* w pliku *applicationHost.config* .</span><span class="sxs-lookup"><span data-stu-id="af73c-785">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="af73c-786">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="af73c-786">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="af73c-787">[Źródło odwołania do modułu ASP.NET Core (gałąź główna)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Użyj listy rozwijanej **rozgałęzienie** , aby wybrać konkretną wersję (na przykład `release/3.1` ).</span><span class="sxs-lookup"><span data-stu-id="af73c-787">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
