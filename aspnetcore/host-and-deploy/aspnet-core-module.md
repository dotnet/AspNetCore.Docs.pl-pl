---
title: Moduł ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak skonfigurować moduł ASP.NET Core do obsługi aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 298d424557600735668217e1ef07ace606dac60b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667301"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="2543f-103">Moduł ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2543f-103">ASP.NET Core Module</span></span>

<span data-ttu-id="2543f-104">[Przez Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti)i [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="2543f-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2543f-105">ASP.NET Core Module to natywny moduł IIS, który podłącza się do potoku IIS do:</span><span class="sxs-lookup"><span data-stu-id="2543f-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="2543f-106">Hostuj aplikację ASP.NET Core wewnątrz procesu roboczego`w3wp.exe`usługi IIS ( ), zwanego [modelem hostingu w trakcie.](#in-process-hosting-model)</span><span class="sxs-lookup"><span data-stu-id="2543f-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="2543f-107">Przesyłanie dalej żądań sieci web do wewnętrznej bazy danych ASP.NET aplikacji Core z [systemem serwera Kestrel,](xref:fundamentals/servers/kestrel)zwanej [modelem hostingu poza procesem.](#out-of-process-hosting-model)</span><span class="sxs-lookup"><span data-stu-id="2543f-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="2543f-108">Obsługiwane wersje systemu Windows:</span><span class="sxs-lookup"><span data-stu-id="2543f-108">Supported Windows versions:</span></span>

* <span data-ttu-id="2543f-109">Windows 7 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="2543f-109">Windows 7 or later</span></span>
* <span data-ttu-id="2543f-110">Windows Server 2008 R2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="2543f-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="2543f-111">Podczas hostingu w procesie moduł używa implementacji serwera w trakcie dla usług IIS, o nazwie IIS HTTP Server (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="2543f-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="2543f-112">Podczas hostingu poza procesem moduł działa tylko z Kestrelem.</span><span class="sxs-lookup"><span data-stu-id="2543f-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="2543f-113">Moduł nie działa z [http.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="2543f-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="2543f-114">Modele hostingu</span><span class="sxs-lookup"><span data-stu-id="2543f-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="2543f-115">Model hostingu w trakcie</span><span class="sxs-lookup"><span data-stu-id="2543f-115">In-process hosting model</span></span>

<span data-ttu-id="2543f-116">ASP.NET aplikacje Core domyślnie do modelu hostingu w trakcie.</span><span class="sxs-lookup"><span data-stu-id="2543f-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="2543f-117">Podczas hostingu w procesie obowiązują następujące cechy:</span><span class="sxs-lookup"><span data-stu-id="2543f-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="2543f-118">Serwer HTTP usług`IISHttpServer`IIS ( ) jest używany zamiast serwera [Kestrel.](xref:fundamentals/servers/kestrel)</span><span class="sxs-lookup"><span data-stu-id="2543f-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="2543f-119">W przypadku w toku [createdefaultbuilder](xref:fundamentals/host/generic-host#default-builder-settings) wywołuje: <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*></span><span class="sxs-lookup"><span data-stu-id="2543f-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="2543f-120">Zarejestruj `IISHttpServer`plik .</span><span class="sxs-lookup"><span data-stu-id="2543f-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="2543f-121">Skonfiguruj port i ścieżkę podstawową, na którą serwer powinien nasłuchiwany podczas uruchamiania za modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2543f-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="2543f-122">Skonfiguruj hosta do przechwytywania błędów uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="2543f-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="2543f-123">[Atrybut requestTimeout](#attributes-of-the-aspnetcore-element) nie ma zastosowania do hostingu w trakcie.</span><span class="sxs-lookup"><span data-stu-id="2543f-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="2543f-124">Udostępnianie puli aplikacji między aplikacjami nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="2543f-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="2543f-125">Użyj jednej puli aplikacji na aplikację.</span><span class="sxs-lookup"><span data-stu-id="2543f-125">Use one app pool per app.</span></span>

* <span data-ttu-id="2543f-126">Podczas korzystania z [narzędzia Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) lub ręcznego umieszczania pliku [app_offline.htm we wdrożeniu](xref:host-and-deploy/iis/index#locked-deployment-files)aplikacja może nie zostać natychmiast zamknięta, jeśli istnieje otwarte połączenie.</span><span class="sxs-lookup"><span data-stu-id="2543f-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="2543f-127">Na przykład połączenie websocket może opóźnić zamknięcie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="2543f-128">Architektura (bitness) aplikacji i zainstalowanego środowiska wykonawczego (x64 lub x86) musi odpowiadać architekturze puli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="2543f-129">Wykryto rozłączenie klienta.</span><span class="sxs-lookup"><span data-stu-id="2543f-129">Client disconnects are detected.</span></span> <span data-ttu-id="2543f-130">Token anulowania [httpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) jest anulowany, gdy klient rozłącza się.</span><span class="sxs-lookup"><span data-stu-id="2543f-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="2543f-131">W ASP.NET Core 2.2.1 lub <xref:System.IO.Directory.GetCurrentDirectory*> starszym zwraca katalog roboczy procesu uruchomionego przez usługi IIS, a nie katalog aplikacji (na przykład *C:\Windows\System32\inetsrv* dla *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="2543f-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="2543f-132">Przykładowy kod, który ustawia bieżący katalog aplikacji, zobacz [CurrentDirectoryHelpers klasy](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="2543f-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="2543f-133">Wywołanie `SetCurrentDirectory` metody.</span><span class="sxs-lookup"><span data-stu-id="2543f-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="2543f-134">Kolejne wywołania w celu <xref:System.IO.Directory.GetCurrentDirectory*> zapewnienia katalogu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="2543f-135">Podczas hostingu w <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> procesie, nie jest wywoływana wewnętrznie, aby zainicjować użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2543f-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="2543f-136">W związku <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> z tym implementacja używana do przekształcania oświadczeń po każdym uwierzytelnianiu nie jest domyślnie aktywowana.</span><span class="sxs-lookup"><span data-stu-id="2543f-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="2543f-137">Podczas przekształcania oświadczeń <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> za <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> pomocą implementacji, wywołanie dodawania usług uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="2543f-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="2543f-138">[Wdrożenia pakietu sieci Web (z jednym plikiem)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) nie są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="2543f-138">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="2543f-139">Model hostingu poza procesem</span><span class="sxs-lookup"><span data-stu-id="2543f-139">Out-of-process hosting model</span></span>

<span data-ttu-id="2543f-140">Aby skonfigurować aplikację do hostingu poza procesem, ustaw `<AspNetCoreHostingModel>` wartość `OutOfProcess` właściwości w pliku projektu (*.csproj*):</span><span class="sxs-lookup"><span data-stu-id="2543f-140">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="2543f-141">Hosting w trakcie jest `InProcess`ustawiony z , który jest wartością domyślną.</span><span class="sxs-lookup"><span data-stu-id="2543f-141">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="2543f-142">Wartość `<AspNetCoreHostingModel>` jest bez uwzględniania wielkości liter, więc `inprocess` i `outofprocess` są prawidłowe wartości.</span><span class="sxs-lookup"><span data-stu-id="2543f-142">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="2543f-143">[Serwer pustułki](xref:fundamentals/servers/kestrel) jest używany zamiast serwera`IISHttpServer`HTTP usług IIS ( ).</span><span class="sxs-lookup"><span data-stu-id="2543f-143">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="2543f-144">W przypadku brakuprocesu [createDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) wywołuje: <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*></span><span class="sxs-lookup"><span data-stu-id="2543f-144">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="2543f-145">Skonfiguruj port i ścieżkę podstawową, na którą serwer powinien nasłuchiwany podczas uruchamiania za modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2543f-145">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="2543f-146">Skonfiguruj hosta do przechwytywania błędów uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="2543f-146">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="2543f-147">Zmiany modelu hostingu</span><span class="sxs-lookup"><span data-stu-id="2543f-147">Hosting model changes</span></span>

<span data-ttu-id="2543f-148">Jeśli `hostingModel` ustawienie zostanie zmienione w pliku *web.config* (wyjaśnione w [sekcji Konfiguracja z web.config),](#configuration-with-webconfig) moduł odtwarza proces roboczy dla usług IIS.</span><span class="sxs-lookup"><span data-stu-id="2543f-148">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="2543f-149">W przypadku usługi IIS Express moduł nie odtwarza procesu roboczego, ale zamiast tego wyzwala pełne wdzięku zamknięcie bieżącego procesu usługi IIS Express.</span><span class="sxs-lookup"><span data-stu-id="2543f-149">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="2543f-150">Następne żądanie do aplikacji tworzy nowy proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="2543f-150">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="2543f-151">Nazwa procesu</span><span class="sxs-lookup"><span data-stu-id="2543f-151">Process name</span></span>

<span data-ttu-id="2543f-152">`Process.GetCurrentProcess().ProcessName``w3wp` / `iisexpress` (w toku) lub `dotnet` (poza procesem).</span><span class="sxs-lookup"><span data-stu-id="2543f-152">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="2543f-153">Wiele modułów natywnych, takich jak uwierzytelnianie systemu Windows, pozostaje aktywnych.</span><span class="sxs-lookup"><span data-stu-id="2543f-153">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="2543f-154">Aby dowiedzieć się więcej o modułach usług IIS <xref:host-and-deploy/iis/modules>aktywnych za pomocą ASP.NET Core Module, zobacz .</span><span class="sxs-lookup"><span data-stu-id="2543f-154">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="2543f-155">ASP.NET Moduł podstawowy może również:</span><span class="sxs-lookup"><span data-stu-id="2543f-155">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="2543f-156">Ustawianie zmiennych środowiskowych dla procesu roboczego.</span><span class="sxs-lookup"><span data-stu-id="2543f-156">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="2543f-157">Dane wyjściowe stdout do magazynu plików w celu rozwiązywania problemów z uruchamianiem.</span><span class="sxs-lookup"><span data-stu-id="2543f-157">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="2543f-158">Przesyłanie dalej tokenów uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="2543f-158">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="2543f-159">Jak zainstalować i korzystać z modułu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2543f-159">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="2543f-160">Aby uzyskać instrukcje dotyczące instalowania modułu ASP.NET Core, zobacz [Instalowanie pakietu hostingowego .NET Core .](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)</span><span class="sxs-lookup"><span data-stu-id="2543f-160">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="2543f-161">Konfiguracja z web.config</span><span class="sxs-lookup"><span data-stu-id="2543f-161">Configuration with web.config</span></span>

<span data-ttu-id="2543f-162">Moduł ASP.NET Core jest skonfigurowany z `aspNetCore` sekcją `system.webServer` węzła w pliku *web.config* witryny.</span><span class="sxs-lookup"><span data-stu-id="2543f-162">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="2543f-163">Następujący plik *web.config* jest publikowany dla [wdrożenia zależnego od struktury](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) i konfiguruje ASP.NET Core Module do obsługi żądań lokacji:</span><span class="sxs-lookup"><span data-stu-id="2543f-163">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="2543f-164">Następujące *web.config* jest publikowany dla [samodzielnego wdrożenia:](/dotnet/articles/core/deploying/#self-contained-deployments-scd)</span><span class="sxs-lookup"><span data-stu-id="2543f-164">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="2543f-165">Właściwość jest <xref:System.Configuration.SectionInformation.InheritInChildApplications*> `false` ustawiona, aby wskazać, że ustawienia określone w [ \<lokalizacji>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element nie są dziedziczone przez aplikacje, które znajdują się w podkatalogu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-165">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="2543f-166">Gdy aplikacja jest wdrażana w [usłudze Azure App Service,](https://azure.microsoft.com/services/app-service/)ścieżka jest ustawiona `stdoutLogFile` na `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="2543f-166">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="2543f-167">Ścieżka zapisuje dzienniki stdout do folderu *LogFiles,* który jest lokalizacją automatycznie utworzoną przez usługę.</span><span class="sxs-lookup"><span data-stu-id="2543f-167">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="2543f-168">Aby uzyskać informacje na temat konfiguracji <xref:host-and-deploy/iis/index#sub-applications>podpołowieczek usług IIS, zobacz .</span><span class="sxs-lookup"><span data-stu-id="2543f-168">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="2543f-169">Atrybuty elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="2543f-169">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="2543f-170">Atrybut</span><span class="sxs-lookup"><span data-stu-id="2543f-170">Attribute</span></span> | <span data-ttu-id="2543f-171">Opis</span><span class="sxs-lookup"><span data-stu-id="2543f-171">Description</span></span> | <span data-ttu-id="2543f-172">Domyślne</span><span class="sxs-lookup"><span data-stu-id="2543f-172">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="2543f-173">Opcjonalny atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="2543f-173">Optional string attribute.</span></span></p><p><span data-ttu-id="2543f-174">Argumenty do pliku wykonywalnego określonego w **processPath**.</span><span class="sxs-lookup"><span data-stu-id="2543f-174">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="2543f-175">Opcjonalny atrybut logiczny.</span><span class="sxs-lookup"><span data-stu-id="2543f-175">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2543f-176">Jeśli true, **502.5 - Błąd procesu** strona jest pomijana, a strona kod stanu 502 skonfigurowana w *web.config* ma pierwszeństwo.</span><span class="sxs-lookup"><span data-stu-id="2543f-176">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="2543f-177">Opcjonalny atrybut logiczny.</span><span class="sxs-lookup"><span data-stu-id="2543f-177">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2543f-178">Jeśli true, token jest przekazywanie dalej do procesu podrzędnego nasłuchiwania na %ASPNETCORE_PORT% jako nagłówek "MS-ASPNETCORE-WINAUTHTOKEN" na żądanie.</span><span class="sxs-lookup"><span data-stu-id="2543f-178">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="2543f-179">Jest odpowiedzialny za tego procesu do wywołania CloseHandle na ten token na żądanie.</span><span class="sxs-lookup"><span data-stu-id="2543f-179">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="2543f-180">Opcjonalny atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="2543f-180">Optional string attribute.</span></span></p><p><span data-ttu-id="2543f-181">Określa model hostingu jako w`InProcess`/`inprocess`trakcie ( ) lub`OutOfProcess`/`outofprocess`poza procesem ( ).</span><span class="sxs-lookup"><span data-stu-id="2543f-181">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="2543f-182">Opcjonalny atrybut liczby całkowitej.</span><span class="sxs-lookup"><span data-stu-id="2543f-182">Optional integer attribute.</span></span></p><p><span data-ttu-id="2543f-183">Określa liczbę wystąpień procesu określonego w **ustawieniu processPath,** które można obrócić na aplikację.</span><span class="sxs-lookup"><span data-stu-id="2543f-183">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="2543f-184">&dagger;W przypadku hostingu w trakcie `1`wartość jest ograniczona do .</span><span class="sxs-lookup"><span data-stu-id="2543f-184">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="2543f-185">Ustawienie `processesPerApplication` jest odradzane.</span><span class="sxs-lookup"><span data-stu-id="2543f-185">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="2543f-186">Ten atrybut zostanie usunięty w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="2543f-186">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="2543f-187">Domyślny:`1`</span><span class="sxs-lookup"><span data-stu-id="2543f-187">Default: `1`</span></span><br><span data-ttu-id="2543f-188">Min:`1`</span><span class="sxs-lookup"><span data-stu-id="2543f-188">Min: `1`</span></span><br><span data-ttu-id="2543f-189">Max:`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="2543f-189">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="2543f-190">Wymagany atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="2543f-190">Required string attribute.</span></span></p><p><span data-ttu-id="2543f-191">Ścieżka do pliku wykonywalnego, który uruchamia proces nasłuchiwania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="2543f-191">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="2543f-192">Ścieżki względne są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="2543f-192">Relative paths are supported.</span></span> <span data-ttu-id="2543f-193">Jeśli ścieżka zaczyna `.`się od , ścieżka jest uważana za względem względem katalogu głównego witryny.</span><span class="sxs-lookup"><span data-stu-id="2543f-193">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="2543f-194">Opcjonalny atrybut liczby całkowitej.</span><span class="sxs-lookup"><span data-stu-id="2543f-194">Optional integer attribute.</span></span></p><p><span data-ttu-id="2543f-195">Określa, ile razy proces określony w **processPath** może ulec awarii na minutę.</span><span class="sxs-lookup"><span data-stu-id="2543f-195">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="2543f-196">Jeśli ten limit zostanie przekroczony, moduł przestaje uruchamiać proces przez pozostałą część minuty.</span><span class="sxs-lookup"><span data-stu-id="2543f-196">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="2543f-197">Nie jest obsługiwany w trakcie hostingu.</span><span class="sxs-lookup"><span data-stu-id="2543f-197">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="2543f-198">Domyślny:`10`</span><span class="sxs-lookup"><span data-stu-id="2543f-198">Default: `10`</span></span><br><span data-ttu-id="2543f-199">Min:`0`</span><span class="sxs-lookup"><span data-stu-id="2543f-199">Min: `0`</span></span><br><span data-ttu-id="2543f-200">Max:`100`</span><span class="sxs-lookup"><span data-stu-id="2543f-200">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="2543f-201">Opcjonalny atrybut przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="2543f-201">Optional timespan attribute.</span></span></p><p><span data-ttu-id="2543f-202">Określa czas trwania oczekiwania modułu ASP.NET Core module na odpowiedź z procesu nasłuchiwania na %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="2543f-202">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="2543f-203">W wersjach modułu ASP.NET Core, który został dostarczony wraz z wydaniem ASP.NET Core 2.1 lub nowszym, `requestTimeout` jest określony w godzinach, minutach i sekundach.</span><span class="sxs-lookup"><span data-stu-id="2543f-203">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="2543f-204">Nie dotyczy hostingu w trakcie.</span><span class="sxs-lookup"><span data-stu-id="2543f-204">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="2543f-205">W przypadku hostingu w trakcie moduł czeka na przetworzenie żądania przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="2543f-205">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="2543f-206">Prawidłowe wartości dla minut i sekund segmentów ciągu znajdują się w zakresie 0-59.</span><span class="sxs-lookup"><span data-stu-id="2543f-206">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="2543f-207">Użycie **60** w wartości minut lub sekund powoduje *500 - Wewnętrzny błąd serwera*.</span><span class="sxs-lookup"><span data-stu-id="2543f-207">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="2543f-208">Domyślny:`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="2543f-208">Default: `00:02:00`</span></span><br><span data-ttu-id="2543f-209">Min:`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="2543f-209">Min: `00:00:00`</span></span><br><span data-ttu-id="2543f-210">Max:`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="2543f-210">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="2543f-211">Opcjonalny atrybut liczby całkowitej.</span><span class="sxs-lookup"><span data-stu-id="2543f-211">Optional integer attribute.</span></span></p><p><span data-ttu-id="2543f-212">Czas trwania w sekundach, że moduł czeka na plik wykonywalny bezpiecznie zamknąć po wykryciu *pliku app_offline.htm.*</span><span class="sxs-lookup"><span data-stu-id="2543f-212">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="2543f-213">Domyślny:`10`</span><span class="sxs-lookup"><span data-stu-id="2543f-213">Default: `10`</span></span><br><span data-ttu-id="2543f-214">Min:`0`</span><span class="sxs-lookup"><span data-stu-id="2543f-214">Min: `0`</span></span><br><span data-ttu-id="2543f-215">Max:`600`</span><span class="sxs-lookup"><span data-stu-id="2543f-215">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="2543f-216">Opcjonalny atrybut liczby całkowitej.</span><span class="sxs-lookup"><span data-stu-id="2543f-216">Optional integer attribute.</span></span></p><p><span data-ttu-id="2543f-217">Czas trwania w sekundach, że moduł czeka na plik wykonywalny, aby rozpocząć proces nasłuchiwania na porcie.</span><span class="sxs-lookup"><span data-stu-id="2543f-217">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="2543f-218">Jeśli ten limit czasu zostanie przekroczony, moduł zabija proces.</span><span class="sxs-lookup"><span data-stu-id="2543f-218">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="2543f-219">Moduł próbuje ponownie uruchomić proces po otrzymaniu nowego żądania i kontynuuje próbę ponownego uruchomienia procesu na kolejne żądania przychodzące, chyba że aplikacja nie może uruchomić **rapidFailsPerMinute** liczba razy w ostatniej toczenia minut.</span><span class="sxs-lookup"><span data-stu-id="2543f-219">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="2543f-220">Wartość 0 (zero) **nie** jest uważany za nieskończony limit czasu.</span><span class="sxs-lookup"><span data-stu-id="2543f-220">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="2543f-221">Domyślny:`120`</span><span class="sxs-lookup"><span data-stu-id="2543f-221">Default: `120`</span></span><br><span data-ttu-id="2543f-222">Min:`0`</span><span class="sxs-lookup"><span data-stu-id="2543f-222">Min: `0`</span></span><br><span data-ttu-id="2543f-223">Max:`3600`</span><span class="sxs-lookup"><span data-stu-id="2543f-223">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="2543f-224">Opcjonalny atrybut logiczny.</span><span class="sxs-lookup"><span data-stu-id="2543f-224">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2543f-225">Jeśli true, **stdout** i **stderr** dla procesu określonego w **processPath** są przekierowywane do pliku określonego w **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="2543f-225">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="2543f-226">Opcjonalny atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="2543f-226">Optional string attribute.</span></span></p><p><span data-ttu-id="2543f-227">Określa względną lub bezwzględną ścieżkę pliku, dla której są rejestrowane **stdout** i **stderr** z procesu określonego w **processPath.**</span><span class="sxs-lookup"><span data-stu-id="2543f-227">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="2543f-228">Ścieżki względne są względem katalogu głównego witryny.</span><span class="sxs-lookup"><span data-stu-id="2543f-228">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="2543f-229">Każda ścieżka `.` rozpoczynająca się względem katalogu głównego witryny i wszystkie inne ścieżki są traktowane jako ścieżki bezwzględne.</span><span class="sxs-lookup"><span data-stu-id="2543f-229">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="2543f-230">Wszystkie foldery znajdujące się w ścieżce są tworzone przez moduł podczas tworzenia pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="2543f-230">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="2543f-231">Za pomocą ograniczników podkreślenia do ostatniego segmentu ścieżki **pliku stdoutLogFile** dodaje się znacznik czasu, identyfikator procesu i rozszerzenie pliku (*.log).*</span><span class="sxs-lookup"><span data-stu-id="2543f-231">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="2543f-232">Jeśli `.\logs\stdout` jest dostarczany jako wartość, przykładowy dziennik stdout jest zapisywany jako *stdout_20180205194132_1934.log* w folderze *dzienników* po zapisaniu w 2/5/2018 o godzinie 19:41:32 z identyfikatorem procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="2543f-232">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="2543f-233">Ustawianie zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="2543f-233">Set environment variables</span></span>

<span data-ttu-id="2543f-234">Zmienne środowiskowe można określić `processPath` dla procesu w atrybucie.</span><span class="sxs-lookup"><span data-stu-id="2543f-234">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="2543f-235">Określ zmienną `<environmentVariable>` środowiskową z `<environmentVariables>` elementem podrzędnym elementu kolekcji.</span><span class="sxs-lookup"><span data-stu-id="2543f-235">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="2543f-236">Zmienne środowiskowe ustawione w tej sekcji mają pierwszeństwo przed zmiennymi środowiska systemowego.</span><span class="sxs-lookup"><span data-stu-id="2543f-236">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="2543f-237">W poniższym przykładzie ustawia się dwie zmienne środowiskowe w *pliku web.config*. `ASPNETCORE_ENVIRONMENT` konfiguruje środowisko aplikacji `Development`na .</span><span class="sxs-lookup"><span data-stu-id="2543f-237">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="2543f-238">Deweloper może tymczasowo ustawić tę wartość w pliku *web.config,* aby wymusić na [stronie wyjątku dewelopera](xref:fundamentals/error-handling) załadować podczas debugowania wyjątku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-238">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="2543f-239">`CONFIG_DIR`jest przykładem zmiennej środowiskowej zdefiniowanej przez użytkownika, gdzie deweloper napisał kod, który odczytuje wartość podczas uruchamiania, tworząc ścieżkę do ładowania pliku konfiguracyjnego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-239">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="2543f-240">Alternatywą dla ustawienia środowiska bezpośrednio w *web.config* jest uwzględnienie `<EnvironmentName>` właściwości w profilu publikowania [(.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) lub pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="2543f-240">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="2543f-241">Takie podejście ustawia środowisko w *web.config* po opublikowaniu projektu:</span><span class="sxs-lookup"><span data-stu-id="2543f-241">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="2543f-242">Zmienną `ASPNETCORE_ENVIRONMENT` środowiskową `Development` należy ustawić tylko na serwery przejściowe i testujące, które nie są dostępne dla niezaufanych sieci, takich jak Internet.</span><span class="sxs-lookup"><span data-stu-id="2543f-242">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="2543f-243">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="2543f-243">app_offline.htm</span></span>

<span data-ttu-id="2543f-244">Jeśli plik o nazwie *app_offline.htm* zostanie wykryty w katalogu głównym aplikacji, moduł ASP.NET Core próbuje bezpiecznie zamknąć aplikację i zatrzymać przetwarzanie żądań przychodzących.</span><span class="sxs-lookup"><span data-stu-id="2543f-244">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="2543f-245">Jeśli aplikacja jest nadal uruchomiona po liczbie `shutdownTimeLimit`sekund zdefiniowanych w , ASP.NET moduł podstawowy zabija uruchomiony proces.</span><span class="sxs-lookup"><span data-stu-id="2543f-245">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="2543f-246">Podczas gdy plik *app_offline.htm* jest obecny, ASP.NET Core Module odpowiada na żądania, wysyłając z powrotem zawartość pliku *app_offline.htm.*</span><span class="sxs-lookup"><span data-stu-id="2543f-246">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="2543f-247">Po usunięciu pliku *app_offline.htm* następne żądanie uruchamia aplikację.</span><span class="sxs-lookup"><span data-stu-id="2543f-247">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="2543f-248">Podczas korzystania z modelu hostingu poza procesem aplikacja może nie zostać natychmiast zamknięta, jeśli istnieje otwarte połączenie.</span><span class="sxs-lookup"><span data-stu-id="2543f-248">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="2543f-249">Na przykład połączenie websocket może opóźnić zamknięcie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-249">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="2543f-250">Strona błędu uruchamiania</span><span class="sxs-lookup"><span data-stu-id="2543f-250">Start-up error page</span></span>

<span data-ttu-id="2543f-251">Hosting w trakcie, jak i poza procesem powoduje tworzenie niestandardowych stron błędów, gdy nie można uruchomić aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-251">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="2543f-252">Jeśli ASP.NET moduł podstawowy nie może znaleźć programu obsługi żądań w toku lub poza procesem, zostanie wyświetlona strona kodu stanu awarii ładowania w trakcie lub poza *procesem.*</span><span class="sxs-lookup"><span data-stu-id="2543f-252">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="2543f-253">W przypadku hostingu w trakcie, jeśli ASP.NET Moduł podstawowy nie może uruchomić aplikacji, zostanie wyświetlona strona kodu stanu *500.30 — Start Failure.*</span><span class="sxs-lookup"><span data-stu-id="2543f-253">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="2543f-254">W przypadku hostingu poza procesem, jeśli ASP.NET modułu rdzenia nie uruchamia procesu wewnętrznej bazy danych lub rozpocznie się proces wewnętrznej bazy danych, ale nie nasłuchuje na skonfigurowanym porcie, zostanie wyświetlona strona kodowa stanu *awarii procesu 502.5.*</span><span class="sxs-lookup"><span data-stu-id="2543f-254">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="2543f-255">Aby pominąć tę stronę i powrócić do domyślnej strony kodowej `disableStartUpErrorPage` stanu IIS 5xx, użyj atrybutu.</span><span class="sxs-lookup"><span data-stu-id="2543f-255">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="2543f-256">Aby uzyskać więcej informacji na temat konfigurowania niestandardowych komunikatów o [błędach, zobacz Błędy \<HTTP httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="2543f-256">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="2543f-257">Tworzenie i przekierowywanie w dzienniku</span><span class="sxs-lookup"><span data-stu-id="2543f-257">Log creation and redirection</span></span>

<span data-ttu-id="2543f-258">Moduł ASP.NET Core przekierowuje wyjście konsoli stdout i stderr na dysk, `stdoutLogEnabled` jeśli ustawiono atrybuty i `stdoutLogFile` atrybuty `aspNetCore` elementu.</span><span class="sxs-lookup"><span data-stu-id="2543f-258">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="2543f-259">Wszystkie foldery `stdoutLogFile` w ścieżce są tworzone przez moduł podczas tworzenia pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="2543f-259">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="2543f-260">Pula aplikacji musi mieć dostęp do zapisu do lokalizacji, w której są zapisywane dzienniki (służy `IIS AppPool\<app_pool_name>` do udzielania uprawnień do zapisu).</span><span class="sxs-lookup"><span data-stu-id="2543f-260">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="2543f-261">Dzienniki nie są obracane, chyba że proces recyklingu /ponownego uruchamiania występuje.</span><span class="sxs-lookup"><span data-stu-id="2543f-261">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="2543f-262">Jest odpowiedzialny za hosta, aby ograniczyć miejsce na dysku dzienniki zużywają.</span><span class="sxs-lookup"><span data-stu-id="2543f-262">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="2543f-263">Używanie dziennika stdout jest zalecane tylko do rozwiązywania problemów z uruchamianiem aplikacji podczas hostingu w serwisie IIS lub podczas korzystania [z obsługi programów IIS w czasie tworzenia w programie Visual Studio,](xref:host-and-deploy/iis/development-time-iis-support)a nie podczas debugowania lokalnie i uruchamiania aplikacji za pomocą usługi IIS Express.</span><span class="sxs-lookup"><span data-stu-id="2543f-263">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="2543f-264">Nie używaj dziennika stdout do ogólnych celów rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-264">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="2543f-265">W przypadku rutynowego rejestrowania w aplikacji ASP.NET Core należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki.</span><span class="sxs-lookup"><span data-stu-id="2543f-265">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2543f-266">Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych firm](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="2543f-266">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="2543f-267">Sygnatura czasowa i rozszerzenie pliku są dodawane automatycznie podczas tworzenia pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="2543f-267">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="2543f-268">Nazwa pliku dziennika składa się przez dołączenie sygnatury czasowej, identyfikatora procesu i `stdoutLogFile` rozszerzenia pliku *(.log*) do ostatniego segmentu ścieżki (zazwyczaj *stdout)* rozdzielonego przez podkreślenia.</span><span class="sxs-lookup"><span data-stu-id="2543f-268">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="2543f-269">Jeśli `stdoutLogFile` ścieżka kończy się *stdout*, dziennik dla aplikacji z PID 1934 utworzony w dniu 2/5/2018 o 19:42:32 ma nazwę pliku *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="2543f-269">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="2543f-270">Jeśli `stdoutLogEnabled` jest false, błędy, które występują podczas uruchamiania aplikacji są przechwytywane i emitowane do dziennika zdarzeń do 30 KB.</span><span class="sxs-lookup"><span data-stu-id="2543f-270">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="2543f-271">Po uruchomieniu wszystkie dodatkowe dzienniki są odrzucane.</span><span class="sxs-lookup"><span data-stu-id="2543f-271">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="2543f-272">Poniższy `aspNetCore` przykładowy element konfiguruje rejestrowanie `.\log\`stdout w ścieżce względnej .</span><span class="sxs-lookup"><span data-stu-id="2543f-272">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="2543f-273">Upewnij się, że tożsamość użytkownika AppPool ma uprawnienia do zapisu w podanej ścieżce.</span><span class="sxs-lookup"><span data-stu-id="2543f-273">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="2543f-274">Podczas publikowania aplikacji dla wdrożenia usługi Azure App Service `stdoutLogFile` zestaw `\\?\%home%\LogFiles\stdout`SDK sieci Web ustawia wartość .</span><span class="sxs-lookup"><span data-stu-id="2543f-274">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="2543f-275">Zmienna środowiskowa `%home` jest wstępnie zdefiniowana dla aplikacji obsługiwanych przez usługę Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="2543f-275">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="2543f-276">Aby utworzyć reguły filtrowania rejestrowania, zobacz sekcje [filtrowania](xref:fundamentals/logging/index#log-filtering) [konfiguracji](xref:fundamentals/logging/index#log-filtering) i dziennika w dokumentacji rejestrowania ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2543f-276">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="2543f-277">Aby uzyskać więcej informacji na temat formatów ścieżek, zobacz [Formaty ścieżek plików w systemach Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="2543f-277">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="2543f-278">Ulepszone dzienniki diagnostyczne</span><span class="sxs-lookup"><span data-stu-id="2543f-278">Enhanced diagnostic logs</span></span>

<span data-ttu-id="2543f-279">Moduł ASP.NET Core można skonfigurować w celu zapewnienia rozszerzonych dzienników diagnostycznych.</span><span class="sxs-lookup"><span data-stu-id="2543f-279">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="2543f-280">Dodaj `<handlerSettings>` element do `<aspNetCore>` elementu w *web.config*. Ustawienie, `debugLevel` `TRACE` aby uwidacznia wyższą wierność informacji diagnostycznych:</span><span class="sxs-lookup"><span data-stu-id="2543f-280">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="2543f-281">Wszystkie foldery w ścieżce *(dzienniki* w poprzednim przykładzie) są tworzone przez moduł podczas tworzenia pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="2543f-281">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="2543f-282">Pula aplikacji musi mieć dostęp do zapisu do lokalizacji, w której są zapisywane dzienniki (służy `IIS AppPool\<app_pool_name>` do udzielania uprawnień do zapisu).</span><span class="sxs-lookup"><span data-stu-id="2543f-282">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="2543f-283">Wartości poziomu`debugLevel`debugowania ( ) mogą obejmować zarówno poziom, jak i lokalizację.</span><span class="sxs-lookup"><span data-stu-id="2543f-283">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="2543f-284">Poziomy (w kolejności od najmniej do większości pełnych):</span><span class="sxs-lookup"><span data-stu-id="2543f-284">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="2543f-285">BŁĄD</span><span class="sxs-lookup"><span data-stu-id="2543f-285">ERROR</span></span>
* <span data-ttu-id="2543f-286">OSTRZEŻENIE</span><span class="sxs-lookup"><span data-stu-id="2543f-286">WARNING</span></span>
* <span data-ttu-id="2543f-287">Informacji</span><span class="sxs-lookup"><span data-stu-id="2543f-287">INFO</span></span>
* <span data-ttu-id="2543f-288">TRACE</span><span class="sxs-lookup"><span data-stu-id="2543f-288">TRACE</span></span>

<span data-ttu-id="2543f-289">Lokalizacje (dozwolone jest wiele lokalizacji):</span><span class="sxs-lookup"><span data-stu-id="2543f-289">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="2543f-290">Konsoli</span><span class="sxs-lookup"><span data-stu-id="2543f-290">CONSOLE</span></span>
* <span data-ttu-id="2543f-291">Eventlog</span><span class="sxs-lookup"><span data-stu-id="2543f-291">EVENTLOG</span></span>
* <span data-ttu-id="2543f-292">PLIK</span><span class="sxs-lookup"><span data-stu-id="2543f-292">FILE</span></span>

<span data-ttu-id="2543f-293">Ustawienia obsługi mogą być również dostarczane za pośrednictwem zmiennych środowiskowych:</span><span class="sxs-lookup"><span data-stu-id="2543f-293">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="2543f-294">`ASPNETCORE_MODULE_DEBUG_FILE`&ndash; Ścieżka do pliku dziennika debugowania.</span><span class="sxs-lookup"><span data-stu-id="2543f-294">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="2543f-295">(Domyślnie: *aspnetcore-debug.log)*</span><span class="sxs-lookup"><span data-stu-id="2543f-295">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="2543f-296">`ASPNETCORE_MODULE_DEBUG`&ndash; Ustawienie poziomu debugowania.</span><span class="sxs-lookup"><span data-stu-id="2543f-296">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="2543f-297">**Nie** należy pozostawiać rejestrowania debugowania włączone we wdrożeniu dłużej niż wymagane do rozwiązania problemu.</span><span class="sxs-lookup"><span data-stu-id="2543f-297">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="2543f-298">Rozmiar dziennika nie jest ograniczony.</span><span class="sxs-lookup"><span data-stu-id="2543f-298">The size of the log isn't limited.</span></span> <span data-ttu-id="2543f-299">Pozostawienie włączonego dziennika debugowania może wyczerpyć dostępne miejsce na dysku i upaść serwer lub usługę aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-299">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="2543f-300">Zobacz [Konfiguracja z web.config](#configuration-with-webconfig) `aspNetCore` na przykład elementu w pliku *web.config.*</span><span class="sxs-lookup"><span data-stu-id="2543f-300">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="2543f-301">Modyfikowanie rozmiaru stosu</span><span class="sxs-lookup"><span data-stu-id="2543f-301">Modify the stack size</span></span>

<span data-ttu-id="2543f-302">*Dotyczy tylko podczas korzystania z modelu hostingu w trakcie.*</span><span class="sxs-lookup"><span data-stu-id="2543f-302">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="2543f-303">Skonfiguruj rozmiar `stackSize` zarządzanego stosu przy użyciu ustawienia w bajtach w *pliku web.config*. Domyślny rozmiar `1048576` to bajty (1 MB).</span><span class="sxs-lookup"><span data-stu-id="2543f-303">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="2543f-304">Konfiguracja serwera proxy używa protokołu HTTP i tokenu parowania</span><span class="sxs-lookup"><span data-stu-id="2543f-304">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="2543f-305">*Dotyczy tylko hostingu poza procesem.*</span><span class="sxs-lookup"><span data-stu-id="2543f-305">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="2543f-306">Serwer proxy utworzony między ASP.NET Core Module a Kestrel używa protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="2543f-306">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="2543f-307">Nie ma ryzyka podsłuchiwania ruchu między modułem a Pustułką z lokalizacji poza serwerem.</span><span class="sxs-lookup"><span data-stu-id="2543f-307">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="2543f-308">Token parowania jest używany do zagwarantowania, że żądania odebrane przez Kestrel zostały wysłane przez usługi IIS i nie pochodzą z innego źródła.</span><span class="sxs-lookup"><span data-stu-id="2543f-308">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="2543f-309">Token parowania jest tworzony i ustawiany`ASPNETCORE_TOKEN`w zmiennej środowiskowej ( ) przez moduł.</span><span class="sxs-lookup"><span data-stu-id="2543f-309">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="2543f-310">Token parowania jest również ustawiony`MS-ASPNETCORE-TOKEN`w nagłówku ( ) przy każdym proxied żądania.</span><span class="sxs-lookup"><span data-stu-id="2543f-310">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="2543f-311">Oprogramowanie pośredniczące usługi IIS sprawdza każde otrzymane żądanie, aby potwierdzić, że wartość nagłówka tokenu parowania jest zgodna z wartością zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="2543f-311">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="2543f-312">Jeśli wartości tokenu są niezgodne, żądanie jest rejestrowane i odrzucane.</span><span class="sxs-lookup"><span data-stu-id="2543f-312">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="2543f-313">Zmienna środowiska tokenu parowania i ruch między modułem a Kestrel nie są dostępne z lokalizacji poza serwerem.</span><span class="sxs-lookup"><span data-stu-id="2543f-313">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="2543f-314">Bez znajomości wartości tokenu parowania osoba atakująca nie może przesłać żądań, które omijają sprawdzanie w oprogramowaniu pośredniczącym usługi IIS.</span><span class="sxs-lookup"><span data-stu-id="2543f-314">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="2543f-315">ASP.NET modułu rdzenia z konfiguracją współdzieloną usług IIS</span><span class="sxs-lookup"><span data-stu-id="2543f-315">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="2543f-316">Instalator modułu ASP.NET Core module działa z uprawnieniami konta **TrustedInstaller.**</span><span class="sxs-lookup"><span data-stu-id="2543f-316">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="2543f-317">Ponieważ lokalne konto systemowe nie ma uprawnień do modyfikowania ścieżki udziału używanej przez konfigurację współużytkową usług IIS, instalator zgłasza błąd odmowy dostępu podczas próby skonfigurowania ustawień modułu w pliku *applicationHost.config* w udziale.</span><span class="sxs-lookup"><span data-stu-id="2543f-317">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="2543f-318">W przypadku korzystania z udostępnionej konfiguracji usług IIS na tym samym komputerze co instalacja `OPT_NO_SHARED_CONFIG_CHECK` usług IIS uruchom instalator ASP.NET Core Hosting Bundle z parametrem ustawionym na: `1`</span><span class="sxs-lookup"><span data-stu-id="2543f-318">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="2543f-319">Jeśli ścieżka do konfiguracji udostępnionej nie jest na tym samym komputerze co instalacja usług IIS, wykonaj następujące kroki:</span><span class="sxs-lookup"><span data-stu-id="2543f-319">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="2543f-320">Wyłącz konfigurację udostępnioną usług IIS.</span><span class="sxs-lookup"><span data-stu-id="2543f-320">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="2543f-321">Uruchom instalatora.</span><span class="sxs-lookup"><span data-stu-id="2543f-321">Run the installer.</span></span>
1. <span data-ttu-id="2543f-322">Wyeksportuj zaktualizowany plik *applicationHost.config* do udziału.</span><span class="sxs-lookup"><span data-stu-id="2543f-322">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="2543f-323">Ponownie włącz konfigurację udostępnioną usług IIS.</span><span class="sxs-lookup"><span data-stu-id="2543f-323">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="2543f-324">Dane dotyczące wersji modułu i dzienników instalatora pakietu hostingowego</span><span class="sxs-lookup"><span data-stu-id="2543f-324">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="2543f-325">Aby określić wersję zainstalowanego ASP.NET modułu rdzenia:</span><span class="sxs-lookup"><span data-stu-id="2543f-325">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="2543f-326">W systemie hostingowym przejdź do *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="2543f-326">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="2543f-327">Znajdź plik *aspnetcore.dll.*</span><span class="sxs-lookup"><span data-stu-id="2543f-327">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="2543f-328">Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Właściwości** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="2543f-328">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="2543f-329">Wybierz kartę **Szczegóły.** **Wersja pliku** i **wersja produktu** reprezentują zainstalowaną wersję modułu.</span><span class="sxs-lookup"><span data-stu-id="2543f-329">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="2543f-330">Dzienniki instalatora pakietu hostingowego dla modułu znajdują się pod adresem *C:\\\\Users %UserName%\\AppData\\Local\\Temp*. Plik nosi nazwę *dd_DotNetCoreWinSvrHosting__\<sygnatura czasowa>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="2543f-330">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="2543f-331">Lokalizacje plików modułów, schematów i konfiguracji</span><span class="sxs-lookup"><span data-stu-id="2543f-331">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="2543f-332">Moduł</span><span class="sxs-lookup"><span data-stu-id="2543f-332">Module</span></span>

<span data-ttu-id="2543f-333">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="2543f-333">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="2543f-334">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2543f-334">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="2543f-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2543f-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="2543f-336">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="2543f-336">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="2543f-337">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="2543f-337">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="2543f-338">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="2543f-338">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="2543f-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2543f-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="2543f-340">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2543f-340">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="2543f-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="2543f-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="2543f-342">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="2543f-342">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="2543f-343">Schemat</span><span class="sxs-lookup"><span data-stu-id="2543f-343">Schema</span></span>

<span data-ttu-id="2543f-344">**IIS**</span><span class="sxs-lookup"><span data-stu-id="2543f-344">**IIS**</span></span>

* <span data-ttu-id="2543f-345">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="2543f-345">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="2543f-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="2543f-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="2543f-347">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="2543f-347">**IIS Express**</span></span>

* <span data-ttu-id="2543f-348">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="2543f-348">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="2543f-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="2543f-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="2543f-350">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="2543f-350">Configuration</span></span>

<span data-ttu-id="2543f-351">**IIS**</span><span class="sxs-lookup"><span data-stu-id="2543f-351">**IIS**</span></span>

* <span data-ttu-id="2543f-352">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="2543f-352">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="2543f-353">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="2543f-353">**IIS Express**</span></span>

* <span data-ttu-id="2543f-354">Visual Studio: {APPLICATION\\ROOT} .vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="2543f-354">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="2543f-355">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="2543f-355">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="2543f-356">Pliki można znaleźć, wyszukując *aspnetcore* w pliku *applicationHost.config.*</span><span class="sxs-lookup"><span data-stu-id="2543f-356">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="2543f-357">ASP.NET Core Module to natywny moduł IIS, który podłącza się do potoku IIS do:</span><span class="sxs-lookup"><span data-stu-id="2543f-357">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="2543f-358">Hostuj aplikację ASP.NET Core wewnątrz procesu roboczego`w3wp.exe`usługi IIS ( ), zwanego [modelem hostingu w trakcie.](#in-process-hosting-model)</span><span class="sxs-lookup"><span data-stu-id="2543f-358">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="2543f-359">Przesyłanie dalej żądań sieci web do wewnętrznej bazy danych ASP.NET aplikacji Core z [systemem serwera Kestrel,](xref:fundamentals/servers/kestrel)zwanej [modelem hostingu poza procesem.](#out-of-process-hosting-model)</span><span class="sxs-lookup"><span data-stu-id="2543f-359">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="2543f-360">Obsługiwane wersje systemu Windows:</span><span class="sxs-lookup"><span data-stu-id="2543f-360">Supported Windows versions:</span></span>

* <span data-ttu-id="2543f-361">Windows 7 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="2543f-361">Windows 7 or later</span></span>
* <span data-ttu-id="2543f-362">Windows Server 2008 R2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="2543f-362">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="2543f-363">Podczas hostingu w procesie moduł używa implementacji serwera w trakcie dla usług IIS, o nazwie IIS HTTP Server (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="2543f-363">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="2543f-364">Podczas hostingu poza procesem moduł działa tylko z Kestrelem.</span><span class="sxs-lookup"><span data-stu-id="2543f-364">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="2543f-365">Moduł nie działa z [http.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="2543f-365">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="2543f-366">Modele hostingu</span><span class="sxs-lookup"><span data-stu-id="2543f-366">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="2543f-367">Model hostingu w trakcie</span><span class="sxs-lookup"><span data-stu-id="2543f-367">In-process hosting model</span></span>

<span data-ttu-id="2543f-368">Aby skonfigurować aplikację do hostingu w `<AspNetCoreHostingModel>` trakcie, dodaj właściwość do pliku `InProcess` projektu aplikacji o wartości (hosting poza procesem jest ustawiony z): `OutOfProcess`</span><span class="sxs-lookup"><span data-stu-id="2543f-368">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="2543f-369">Model hostingu w trakcie nie jest obsługiwany dla aplikacji ASP.NET Core, które są przeznaczone dla platformy .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="2543f-369">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="2543f-370">Wartość `<AspNetCoreHostingModel>` jest bez uwzględniania wielkości liter, więc `inprocess` i `outofprocess` są prawidłowe wartości.</span><span class="sxs-lookup"><span data-stu-id="2543f-370">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="2543f-371">Jeśli `<AspNetCoreHostingModel>` właściwości nie ma w pliku, wartością `OutOfProcess`domyślną jest .</span><span class="sxs-lookup"><span data-stu-id="2543f-371">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="2543f-372">Podczas hostingu w procesie obowiązują następujące cechy:</span><span class="sxs-lookup"><span data-stu-id="2543f-372">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="2543f-373">Serwer HTTP usług`IISHttpServer`IIS ( ) jest używany zamiast serwera [Kestrel.](xref:fundamentals/servers/kestrel)</span><span class="sxs-lookup"><span data-stu-id="2543f-373">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="2543f-374">W przypadku w toku [createdefaultbuilder](xref:fundamentals/host/web-host#set-up-a-host) wywołuje: <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*></span><span class="sxs-lookup"><span data-stu-id="2543f-374">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="2543f-375">Zarejestruj `IISHttpServer`plik .</span><span class="sxs-lookup"><span data-stu-id="2543f-375">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="2543f-376">Skonfiguruj port i ścieżkę podstawową, na którą serwer powinien nasłuchiwany podczas uruchamiania za modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2543f-376">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="2543f-377">Skonfiguruj hosta do przechwytywania błędów uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="2543f-377">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="2543f-378">[Atrybut requestTimeout](#attributes-of-the-aspnetcore-element) nie ma zastosowania do hostingu w trakcie.</span><span class="sxs-lookup"><span data-stu-id="2543f-378">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="2543f-379">Udostępnianie puli aplikacji między aplikacjami nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="2543f-379">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="2543f-380">Użyj jednej puli aplikacji na aplikację.</span><span class="sxs-lookup"><span data-stu-id="2543f-380">Use one app pool per app.</span></span>

* <span data-ttu-id="2543f-381">Podczas korzystania z [narzędzia Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) lub ręcznego umieszczania pliku [app_offline.htm we wdrożeniu](xref:host-and-deploy/iis/index#locked-deployment-files)aplikacja może nie zostać natychmiast zamknięta, jeśli istnieje otwarte połączenie.</span><span class="sxs-lookup"><span data-stu-id="2543f-381">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="2543f-382">Na przykład połączenie websocket może opóźnić zamknięcie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-382">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="2543f-383">Architektura (bitness) aplikacji i zainstalowanego środowiska wykonawczego (x64 lub x86) musi odpowiadać architekturze puli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-383">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="2543f-384">Wykryto rozłączenie klienta.</span><span class="sxs-lookup"><span data-stu-id="2543f-384">Client disconnects are detected.</span></span> <span data-ttu-id="2543f-385">Token anulowania [httpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) jest anulowany, gdy klient rozłącza się.</span><span class="sxs-lookup"><span data-stu-id="2543f-385">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="2543f-386">W ASP.NET Core 2.2.1 lub <xref:System.IO.Directory.GetCurrentDirectory*> starszym zwraca katalog roboczy procesu uruchomionego przez usługi IIS, a nie katalog aplikacji (na przykład *C:\Windows\System32\inetsrv* dla *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="2543f-386">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="2543f-387">Przykładowy kod, który ustawia bieżący katalog aplikacji, zobacz [CurrentDirectoryHelpers klasy](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="2543f-387">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="2543f-388">Wywołanie `SetCurrentDirectory` metody.</span><span class="sxs-lookup"><span data-stu-id="2543f-388">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="2543f-389">Kolejne wywołania w celu <xref:System.IO.Directory.GetCurrentDirectory*> zapewnienia katalogu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-389">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="2543f-390">Podczas hostingu w <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> procesie, nie jest wywoływana wewnętrznie, aby zainicjować użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2543f-390">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="2543f-391">W związku <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> z tym implementacja używana do przekształcania oświadczeń po każdym uwierzytelnianiu nie jest domyślnie aktywowana.</span><span class="sxs-lookup"><span data-stu-id="2543f-391">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="2543f-392">Podczas przekształcania oświadczeń <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> za <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> pomocą implementacji, wywołanie dodawania usług uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="2543f-392">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="2543f-393">Model hostingu poza procesem</span><span class="sxs-lookup"><span data-stu-id="2543f-393">Out-of-process hosting model</span></span>

<span data-ttu-id="2543f-394">Aby skonfigurować aplikację do hostingu poza procesem, użyj jednego z następujących metod w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="2543f-394">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="2543f-395">Nie określaj `<AspNetCoreHostingModel>` właściwości.</span><span class="sxs-lookup"><span data-stu-id="2543f-395">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="2543f-396">Jeśli `<AspNetCoreHostingModel>` właściwości nie ma w pliku, wartością `OutOfProcess`domyślną jest .</span><span class="sxs-lookup"><span data-stu-id="2543f-396">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="2543f-397">Ustaw wartość `<AspNetCoreHostingModel>` właściwości (hosting `OutOfProcess` w trakcie jest `InProcess`ustawiony z):</span><span class="sxs-lookup"><span data-stu-id="2543f-397">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="2543f-398">Wartość jest bez uwzględniania wielkości `inprocess` liter, więc i `outofprocess` są prawidłowe wartości.</span><span class="sxs-lookup"><span data-stu-id="2543f-398">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="2543f-399">[Serwer pustułki](xref:fundamentals/servers/kestrel) jest używany zamiast serwera`IISHttpServer`HTTP usług IIS ( ).</span><span class="sxs-lookup"><span data-stu-id="2543f-399">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="2543f-400">W przypadku brakuprocesu [createDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) wywołuje: <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*></span><span class="sxs-lookup"><span data-stu-id="2543f-400">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="2543f-401">Skonfiguruj port i ścieżkę podstawową, na którą serwer powinien nasłuchiwany podczas uruchamiania za modułem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2543f-401">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="2543f-402">Skonfiguruj hosta do przechwytywania błędów uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="2543f-402">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="2543f-403">Zmiany modelu hostingu</span><span class="sxs-lookup"><span data-stu-id="2543f-403">Hosting model changes</span></span>

<span data-ttu-id="2543f-404">Jeśli `hostingModel` ustawienie zostanie zmienione w pliku *web.config* (wyjaśnione w [sekcji Konfiguracja z web.config),](#configuration-with-webconfig) moduł odtwarza proces roboczy dla usług IIS.</span><span class="sxs-lookup"><span data-stu-id="2543f-404">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="2543f-405">W przypadku usługi IIS Express moduł nie odtwarza procesu roboczego, ale zamiast tego wyzwala pełne wdzięku zamknięcie bieżącego procesu usługi IIS Express.</span><span class="sxs-lookup"><span data-stu-id="2543f-405">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="2543f-406">Następne żądanie do aplikacji tworzy nowy proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="2543f-406">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="2543f-407">Nazwa procesu</span><span class="sxs-lookup"><span data-stu-id="2543f-407">Process name</span></span>

<span data-ttu-id="2543f-408">`Process.GetCurrentProcess().ProcessName``w3wp` / `iisexpress` (w toku) lub `dotnet` (poza procesem).</span><span class="sxs-lookup"><span data-stu-id="2543f-408">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="2543f-409">Wiele modułów natywnych, takich jak uwierzytelnianie systemu Windows, pozostaje aktywnych.</span><span class="sxs-lookup"><span data-stu-id="2543f-409">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="2543f-410">Aby dowiedzieć się więcej o modułach usług IIS <xref:host-and-deploy/iis/modules>aktywnych za pomocą ASP.NET Core Module, zobacz .</span><span class="sxs-lookup"><span data-stu-id="2543f-410">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="2543f-411">ASP.NET Moduł podstawowy może również:</span><span class="sxs-lookup"><span data-stu-id="2543f-411">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="2543f-412">Ustawianie zmiennych środowiskowych dla procesu roboczego.</span><span class="sxs-lookup"><span data-stu-id="2543f-412">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="2543f-413">Dane wyjściowe stdout do magazynu plików w celu rozwiązywania problemów z uruchamianiem.</span><span class="sxs-lookup"><span data-stu-id="2543f-413">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="2543f-414">Przesyłanie dalej tokenów uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="2543f-414">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="2543f-415">Jak zainstalować i korzystać z modułu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2543f-415">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="2543f-416">Aby uzyskać instrukcje dotyczące instalowania modułu ASP.NET Core, zobacz [Instalowanie pakietu hostingowego .NET Core .](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)</span><span class="sxs-lookup"><span data-stu-id="2543f-416">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="2543f-417">Konfiguracja z web.config</span><span class="sxs-lookup"><span data-stu-id="2543f-417">Configuration with web.config</span></span>

<span data-ttu-id="2543f-418">Moduł ASP.NET Core jest skonfigurowany z `aspNetCore` sekcją `system.webServer` węzła w pliku *web.config* witryny.</span><span class="sxs-lookup"><span data-stu-id="2543f-418">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="2543f-419">Następujący plik *web.config* jest publikowany dla [wdrożenia zależnego od struktury](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) i konfiguruje ASP.NET Core Module do obsługi żądań lokacji:</span><span class="sxs-lookup"><span data-stu-id="2543f-419">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="2543f-420">Następujące *web.config* jest publikowany dla [samodzielnego wdrożenia:](/dotnet/articles/core/deploying/#self-contained-deployments-scd)</span><span class="sxs-lookup"><span data-stu-id="2543f-420">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="2543f-421">Właściwość jest <xref:System.Configuration.SectionInformation.InheritInChildApplications*> `false` ustawiona, aby wskazać, że ustawienia określone w [ \<lokalizacji>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element nie są dziedziczone przez aplikacje, które znajdują się w podkatalogu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-421">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="2543f-422">Gdy aplikacja jest wdrażana w [usłudze Azure App Service,](https://azure.microsoft.com/services/app-service/)ścieżka jest ustawiona `stdoutLogFile` na `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="2543f-422">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="2543f-423">Ścieżka zapisuje dzienniki stdout do folderu *LogFiles,* który jest lokalizacją automatycznie utworzoną przez usługę.</span><span class="sxs-lookup"><span data-stu-id="2543f-423">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="2543f-424">Aby uzyskać informacje na temat konfiguracji <xref:host-and-deploy/iis/index#sub-applications>podpołowieczek usług IIS, zobacz .</span><span class="sxs-lookup"><span data-stu-id="2543f-424">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="2543f-425">Atrybuty elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="2543f-425">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="2543f-426">Atrybut</span><span class="sxs-lookup"><span data-stu-id="2543f-426">Attribute</span></span> | <span data-ttu-id="2543f-427">Opis</span><span class="sxs-lookup"><span data-stu-id="2543f-427">Description</span></span> | <span data-ttu-id="2543f-428">Domyślne</span><span class="sxs-lookup"><span data-stu-id="2543f-428">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="2543f-429">Opcjonalny atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="2543f-429">Optional string attribute.</span></span></p><p><span data-ttu-id="2543f-430">Argumenty do pliku wykonywalnego określonego w **processPath**.</span><span class="sxs-lookup"><span data-stu-id="2543f-430">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="2543f-431">Opcjonalny atrybut logiczny.</span><span class="sxs-lookup"><span data-stu-id="2543f-431">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2543f-432">Jeśli true, **502.5 - Błąd procesu** strona jest pomijana, a strona kod stanu 502 skonfigurowana w *web.config* ma pierwszeństwo.</span><span class="sxs-lookup"><span data-stu-id="2543f-432">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="2543f-433">Opcjonalny atrybut logiczny.</span><span class="sxs-lookup"><span data-stu-id="2543f-433">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2543f-434">Jeśli true, token jest przekazywanie dalej do procesu podrzędnego nasłuchiwania na %ASPNETCORE_PORT% jako nagłówek "MS-ASPNETCORE-WINAUTHTOKEN" na żądanie.</span><span class="sxs-lookup"><span data-stu-id="2543f-434">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="2543f-435">Jest odpowiedzialny za tego procesu do wywołania CloseHandle na ten token na żądanie.</span><span class="sxs-lookup"><span data-stu-id="2543f-435">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="2543f-436">Opcjonalny atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="2543f-436">Optional string attribute.</span></span></p><p><span data-ttu-id="2543f-437">Określa model hostingu jako w`InProcess`/`inprocess`trakcie ( ) lub`OutOfProcess`/`outofprocess`poza procesem ( ).</span><span class="sxs-lookup"><span data-stu-id="2543f-437">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="2543f-438">Opcjonalny atrybut liczby całkowitej.</span><span class="sxs-lookup"><span data-stu-id="2543f-438">Optional integer attribute.</span></span></p><p><span data-ttu-id="2543f-439">Określa liczbę wystąpień procesu określonego w **ustawieniu processPath,** które można obrócić na aplikację.</span><span class="sxs-lookup"><span data-stu-id="2543f-439">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="2543f-440">&dagger;W przypadku hostingu w trakcie `1`wartość jest ograniczona do .</span><span class="sxs-lookup"><span data-stu-id="2543f-440">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="2543f-441">Ustawienie `processesPerApplication` jest odradzane.</span><span class="sxs-lookup"><span data-stu-id="2543f-441">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="2543f-442">Ten atrybut zostanie usunięty w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="2543f-442">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="2543f-443">Domyślny:`1`</span><span class="sxs-lookup"><span data-stu-id="2543f-443">Default: `1`</span></span><br><span data-ttu-id="2543f-444">Min:`1`</span><span class="sxs-lookup"><span data-stu-id="2543f-444">Min: `1`</span></span><br><span data-ttu-id="2543f-445">Max:`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="2543f-445">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="2543f-446">Wymagany atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="2543f-446">Required string attribute.</span></span></p><p><span data-ttu-id="2543f-447">Ścieżka do pliku wykonywalnego, który uruchamia proces nasłuchiwania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="2543f-447">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="2543f-448">Ścieżki względne są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="2543f-448">Relative paths are supported.</span></span> <span data-ttu-id="2543f-449">Jeśli ścieżka zaczyna `.`się od , ścieżka jest uważana za względem względem katalogu głównego witryny.</span><span class="sxs-lookup"><span data-stu-id="2543f-449">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="2543f-450">Opcjonalny atrybut liczby całkowitej.</span><span class="sxs-lookup"><span data-stu-id="2543f-450">Optional integer attribute.</span></span></p><p><span data-ttu-id="2543f-451">Określa, ile razy proces określony w **processPath** może ulec awarii na minutę.</span><span class="sxs-lookup"><span data-stu-id="2543f-451">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="2543f-452">Jeśli ten limit zostanie przekroczony, moduł przestaje uruchamiać proces przez pozostałą część minuty.</span><span class="sxs-lookup"><span data-stu-id="2543f-452">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="2543f-453">Nie jest obsługiwany w trakcie hostingu.</span><span class="sxs-lookup"><span data-stu-id="2543f-453">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="2543f-454">Domyślny:`10`</span><span class="sxs-lookup"><span data-stu-id="2543f-454">Default: `10`</span></span><br><span data-ttu-id="2543f-455">Min:`0`</span><span class="sxs-lookup"><span data-stu-id="2543f-455">Min: `0`</span></span><br><span data-ttu-id="2543f-456">Max:`100`</span><span class="sxs-lookup"><span data-stu-id="2543f-456">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="2543f-457">Opcjonalny atrybut przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="2543f-457">Optional timespan attribute.</span></span></p><p><span data-ttu-id="2543f-458">Określa czas trwania oczekiwania modułu ASP.NET Core module na odpowiedź z procesu nasłuchiwania na %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="2543f-458">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="2543f-459">W wersjach modułu ASP.NET Core, który został dostarczony wraz z wydaniem ASP.NET Core 2.1 lub nowszym, `requestTimeout` jest określony w godzinach, minutach i sekundach.</span><span class="sxs-lookup"><span data-stu-id="2543f-459">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="2543f-460">Nie dotyczy hostingu w trakcie.</span><span class="sxs-lookup"><span data-stu-id="2543f-460">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="2543f-461">W przypadku hostingu w trakcie moduł czeka na przetworzenie żądania przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="2543f-461">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="2543f-462">Prawidłowe wartości dla minut i sekund segmentów ciągu znajdują się w zakresie 0-59.</span><span class="sxs-lookup"><span data-stu-id="2543f-462">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="2543f-463">Użycie **60** w wartości minut lub sekund powoduje *500 - Wewnętrzny błąd serwera*.</span><span class="sxs-lookup"><span data-stu-id="2543f-463">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="2543f-464">Domyślny:`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="2543f-464">Default: `00:02:00`</span></span><br><span data-ttu-id="2543f-465">Min:`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="2543f-465">Min: `00:00:00`</span></span><br><span data-ttu-id="2543f-466">Max:`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="2543f-466">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="2543f-467">Opcjonalny atrybut liczby całkowitej.</span><span class="sxs-lookup"><span data-stu-id="2543f-467">Optional integer attribute.</span></span></p><p><span data-ttu-id="2543f-468">Czas trwania w sekundach, że moduł czeka na plik wykonywalny bezpiecznie zamknąć po wykryciu *pliku app_offline.htm.*</span><span class="sxs-lookup"><span data-stu-id="2543f-468">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="2543f-469">Domyślny:`10`</span><span class="sxs-lookup"><span data-stu-id="2543f-469">Default: `10`</span></span><br><span data-ttu-id="2543f-470">Min:`0`</span><span class="sxs-lookup"><span data-stu-id="2543f-470">Min: `0`</span></span><br><span data-ttu-id="2543f-471">Max:`600`</span><span class="sxs-lookup"><span data-stu-id="2543f-471">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="2543f-472">Opcjonalny atrybut liczby całkowitej.</span><span class="sxs-lookup"><span data-stu-id="2543f-472">Optional integer attribute.</span></span></p><p><span data-ttu-id="2543f-473">Czas trwania w sekundach, że moduł czeka na plik wykonywalny, aby rozpocząć proces nasłuchiwania na porcie.</span><span class="sxs-lookup"><span data-stu-id="2543f-473">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="2543f-474">Jeśli ten limit czasu zostanie przekroczony, moduł zabija proces.</span><span class="sxs-lookup"><span data-stu-id="2543f-474">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="2543f-475">Moduł próbuje ponownie uruchomić proces po otrzymaniu nowego żądania i kontynuuje próbę ponownego uruchomienia procesu na kolejne żądania przychodzące, chyba że aplikacja nie może uruchomić **rapidFailsPerMinute** liczba razy w ostatniej toczenia minut.</span><span class="sxs-lookup"><span data-stu-id="2543f-475">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="2543f-476">Wartość 0 (zero) **nie** jest uważany za nieskończony limit czasu.</span><span class="sxs-lookup"><span data-stu-id="2543f-476">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="2543f-477">Domyślny:`120`</span><span class="sxs-lookup"><span data-stu-id="2543f-477">Default: `120`</span></span><br><span data-ttu-id="2543f-478">Min:`0`</span><span class="sxs-lookup"><span data-stu-id="2543f-478">Min: `0`</span></span><br><span data-ttu-id="2543f-479">Max:`3600`</span><span class="sxs-lookup"><span data-stu-id="2543f-479">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="2543f-480">Opcjonalny atrybut logiczny.</span><span class="sxs-lookup"><span data-stu-id="2543f-480">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2543f-481">Jeśli true, **stdout** i **stderr** dla procesu określonego w **processPath** są przekierowywane do pliku określonego w **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="2543f-481">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="2543f-482">Opcjonalny atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="2543f-482">Optional string attribute.</span></span></p><p><span data-ttu-id="2543f-483">Określa względną lub bezwzględną ścieżkę pliku, dla której są rejestrowane **stdout** i **stderr** z procesu określonego w **processPath.**</span><span class="sxs-lookup"><span data-stu-id="2543f-483">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="2543f-484">Ścieżki względne są względem katalogu głównego witryny.</span><span class="sxs-lookup"><span data-stu-id="2543f-484">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="2543f-485">Każda ścieżka `.` rozpoczynająca się względem katalogu głównego witryny i wszystkie inne ścieżki są traktowane jako ścieżki bezwzględne.</span><span class="sxs-lookup"><span data-stu-id="2543f-485">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="2543f-486">Wszystkie foldery znajdujące się w ścieżce są tworzone przez moduł podczas tworzenia pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="2543f-486">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="2543f-487">Za pomocą ograniczników podkreślenia do ostatniego segmentu ścieżki **pliku stdoutLogFile** dodaje się znacznik czasu, identyfikator procesu i rozszerzenie pliku (*.log).*</span><span class="sxs-lookup"><span data-stu-id="2543f-487">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="2543f-488">Jeśli `.\logs\stdout` jest dostarczany jako wartość, przykładowy dziennik stdout jest zapisywany jako *stdout_20180205194132_1934.log* w folderze *dzienników* po zapisaniu w 2/5/2018 o godzinie 19:41:32 z identyfikatorem procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="2543f-488">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="2543f-489">Ustawianie zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="2543f-489">Setting environment variables</span></span>

<span data-ttu-id="2543f-490">Zmienne środowiskowe można określić `processPath` dla procesu w atrybucie.</span><span class="sxs-lookup"><span data-stu-id="2543f-490">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="2543f-491">Określ zmienną `<environmentVariable>` środowiskową z `<environmentVariables>` elementem podrzędnym elementu kolekcji.</span><span class="sxs-lookup"><span data-stu-id="2543f-491">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="2543f-492">Zmienne środowiskowe ustawione w tej sekcji mają pierwszeństwo przed zmiennymi środowiska systemowego.</span><span class="sxs-lookup"><span data-stu-id="2543f-492">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="2543f-493">W poniższym przykładzie ustawia dwie zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="2543f-493">The following example sets two environment variables.</span></span> <span data-ttu-id="2543f-494">`ASPNETCORE_ENVIRONMENT`konfiguruje środowisko aplikacji `Development`na .</span><span class="sxs-lookup"><span data-stu-id="2543f-494">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="2543f-495">Deweloper może tymczasowo ustawić tę wartość w pliku *web.config,* aby wymusić na [stronie wyjątku dewelopera](xref:fundamentals/error-handling) załadować podczas debugowania wyjątku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-495">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="2543f-496">`CONFIG_DIR`jest przykładem zmiennej środowiskowej zdefiniowanej przez użytkownika, gdzie deweloper napisał kod, który odczytuje wartość podczas uruchamiania, tworząc ścieżkę do ładowania pliku konfiguracyjnego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-496">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="2543f-497">Alternatywą dla ustawienia środowiska bezpośrednio w *web.config* jest uwzględnienie `<EnvironmentName>` właściwości w profilu publikowania [(.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) lub pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="2543f-497">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="2543f-498">Takie podejście ustawia środowisko w *web.config* po opublikowaniu projektu:</span><span class="sxs-lookup"><span data-stu-id="2543f-498">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="2543f-499">Zmienną `ASPNETCORE_ENVIRONMENT` środowiskową `Development` należy ustawić tylko na serwery przejściowe i testujące, które nie są dostępne dla niezaufanych sieci, takich jak Internet.</span><span class="sxs-lookup"><span data-stu-id="2543f-499">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="2543f-500">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="2543f-500">app_offline.htm</span></span>

<span data-ttu-id="2543f-501">Jeśli plik o nazwie *app_offline.htm* zostanie wykryty w katalogu głównym aplikacji, moduł ASP.NET Core próbuje bezpiecznie zamknąć aplikację i zatrzymać przetwarzanie żądań przychodzących.</span><span class="sxs-lookup"><span data-stu-id="2543f-501">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="2543f-502">Jeśli aplikacja jest nadal uruchomiona po liczbie `shutdownTimeLimit`sekund zdefiniowanych w , ASP.NET moduł podstawowy zabija uruchomiony proces.</span><span class="sxs-lookup"><span data-stu-id="2543f-502">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="2543f-503">Podczas gdy plik *app_offline.htm* jest obecny, ASP.NET Core Module odpowiada na żądania, wysyłając z powrotem zawartość pliku *app_offline.htm.*</span><span class="sxs-lookup"><span data-stu-id="2543f-503">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="2543f-504">Po usunięciu pliku *app_offline.htm* następne żądanie uruchamia aplikację.</span><span class="sxs-lookup"><span data-stu-id="2543f-504">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="2543f-505">Podczas korzystania z modelu hostingu poza procesem aplikacja może nie zostać natychmiast zamknięta, jeśli istnieje otwarte połączenie.</span><span class="sxs-lookup"><span data-stu-id="2543f-505">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="2543f-506">Na przykład połączenie websocket może opóźnić zamknięcie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-506">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="2543f-507">Strona błędu uruchamiania</span><span class="sxs-lookup"><span data-stu-id="2543f-507">Start-up error page</span></span>

<span data-ttu-id="2543f-508">Hosting w trakcie, jak i poza procesem powoduje tworzenie niestandardowych stron błędów, gdy nie można uruchomić aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-508">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="2543f-509">Jeśli ASP.NET moduł podstawowy nie może znaleźć programu obsługi żądań w toku lub poza procesem, zostanie wyświetlona strona kodu stanu awarii ładowania w trakcie lub poza *procesem.*</span><span class="sxs-lookup"><span data-stu-id="2543f-509">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="2543f-510">W przypadku hostingu w trakcie, jeśli ASP.NET Moduł podstawowy nie może uruchomić aplikacji, zostanie wyświetlona strona kodu stanu *500.30 — Start Failure.*</span><span class="sxs-lookup"><span data-stu-id="2543f-510">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="2543f-511">W przypadku hostingu poza procesem, jeśli ASP.NET modułu rdzenia nie uruchamia procesu wewnętrznej bazy danych lub rozpocznie się proces wewnętrznej bazy danych, ale nie nasłuchuje na skonfigurowanym porcie, zostanie wyświetlona strona kodowa stanu *awarii procesu 502.5.*</span><span class="sxs-lookup"><span data-stu-id="2543f-511">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="2543f-512">Aby pominąć tę stronę i powrócić do domyślnej strony kodowej `disableStartUpErrorPage` stanu IIS 5xx, użyj atrybutu.</span><span class="sxs-lookup"><span data-stu-id="2543f-512">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="2543f-513">Aby uzyskać więcej informacji na temat konfigurowania niestandardowych komunikatów o [błędach, zobacz Błędy \<HTTP httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="2543f-513">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="2543f-514">Tworzenie i przekierowywanie w dzienniku</span><span class="sxs-lookup"><span data-stu-id="2543f-514">Log creation and redirection</span></span>

<span data-ttu-id="2543f-515">Moduł ASP.NET Core przekierowuje wyjście konsoli stdout i stderr na dysk, `stdoutLogEnabled` jeśli ustawiono atrybuty i `stdoutLogFile` atrybuty `aspNetCore` elementu.</span><span class="sxs-lookup"><span data-stu-id="2543f-515">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="2543f-516">Wszystkie foldery `stdoutLogFile` w ścieżce są tworzone przez moduł podczas tworzenia pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="2543f-516">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="2543f-517">Pula aplikacji musi mieć dostęp do zapisu do lokalizacji, w której są zapisywane dzienniki (służy `IIS AppPool\<app_pool_name>` do udzielania uprawnień do zapisu).</span><span class="sxs-lookup"><span data-stu-id="2543f-517">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="2543f-518">Dzienniki nie są obracane, chyba że proces recyklingu /ponownego uruchamiania występuje.</span><span class="sxs-lookup"><span data-stu-id="2543f-518">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="2543f-519">Jest odpowiedzialny za hosta, aby ograniczyć miejsce na dysku dzienniki zużywają.</span><span class="sxs-lookup"><span data-stu-id="2543f-519">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="2543f-520">Używanie dziennika stdout jest zalecane tylko do rozwiązywania problemów z uruchamianiem aplikacji podczas hostingu w serwisie IIS lub podczas korzystania [z obsługi programów IIS w czasie tworzenia w programie Visual Studio,](xref:host-and-deploy/iis/development-time-iis-support)a nie podczas debugowania lokalnie i uruchamiania aplikacji za pomocą usługi IIS Express.</span><span class="sxs-lookup"><span data-stu-id="2543f-520">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="2543f-521">Nie używaj dziennika stdout do ogólnych celów rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-521">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="2543f-522">W przypadku rutynowego rejestrowania w aplikacji ASP.NET Core należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki.</span><span class="sxs-lookup"><span data-stu-id="2543f-522">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2543f-523">Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych firm](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="2543f-523">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="2543f-524">Sygnatura czasowa i rozszerzenie pliku są dodawane automatycznie podczas tworzenia pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="2543f-524">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="2543f-525">Nazwa pliku dziennika składa się przez dołączenie sygnatury czasowej, identyfikatora procesu i `stdoutLogFile` rozszerzenia pliku *(.log*) do ostatniego segmentu ścieżki (zazwyczaj *stdout)* rozdzielonego przez podkreślenia.</span><span class="sxs-lookup"><span data-stu-id="2543f-525">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="2543f-526">Jeśli `stdoutLogFile` ścieżka kończy się *stdout*, dziennik dla aplikacji z PID 1934 utworzony w dniu 2/5/2018 o 19:42:32 ma nazwę pliku *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="2543f-526">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="2543f-527">Jeśli `stdoutLogEnabled` jest false, błędy, które występują podczas uruchamiania aplikacji są przechwytywane i emitowane do dziennika zdarzeń do 30 KB.</span><span class="sxs-lookup"><span data-stu-id="2543f-527">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="2543f-528">Po uruchomieniu wszystkie dodatkowe dzienniki są odrzucane.</span><span class="sxs-lookup"><span data-stu-id="2543f-528">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="2543f-529">Poniższy `aspNetCore` przykładowy element konfiguruje rejestrowanie `.\log\`stdout w ścieżce względnej .</span><span class="sxs-lookup"><span data-stu-id="2543f-529">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="2543f-530">Upewnij się, że tożsamość użytkownika AppPool ma uprawnienia do zapisu w podanej ścieżce.</span><span class="sxs-lookup"><span data-stu-id="2543f-530">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="2543f-531">Podczas publikowania aplikacji dla wdrożenia usługi Azure App Service `stdoutLogFile` zestaw `\\?\%home%\LogFiles\stdout`SDK sieci Web ustawia wartość .</span><span class="sxs-lookup"><span data-stu-id="2543f-531">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="2543f-532">Zmienna środowiskowa `%home` jest wstępnie zdefiniowana dla aplikacji obsługiwanych przez usługę Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="2543f-532">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="2543f-533">Aby uzyskać więcej informacji na temat formatów ścieżek, zobacz [Formaty ścieżek plików w systemach Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="2543f-533">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="2543f-534">Ulepszone dzienniki diagnostyczne</span><span class="sxs-lookup"><span data-stu-id="2543f-534">Enhanced diagnostic logs</span></span>

<span data-ttu-id="2543f-535">Moduł ASP.NET Core można skonfigurować w celu zapewnienia rozszerzonych dzienników diagnostycznych.</span><span class="sxs-lookup"><span data-stu-id="2543f-535">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="2543f-536">Dodaj `<handlerSettings>` element do `<aspNetCore>` elementu w *web.config*. Ustawienie, `debugLevel` `TRACE` aby uwidacznia wyższą wierność informacji diagnostycznych:</span><span class="sxs-lookup"><span data-stu-id="2543f-536">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="2543f-537">Foldery w ścieżce `<handlerSetting>` dostarczonej do wartości *(dzienniki* w poprzednim przykładzie) nie są tworzone przez moduł automatycznie i powinny wcześniej istnieć we wdrożeniu.</span><span class="sxs-lookup"><span data-stu-id="2543f-537">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="2543f-538">Pula aplikacji musi mieć dostęp do zapisu do lokalizacji, w której są zapisywane dzienniki (służy `IIS AppPool\<app_pool_name>` do udzielania uprawnień do zapisu).</span><span class="sxs-lookup"><span data-stu-id="2543f-538">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="2543f-539">Wartości poziomu`debugLevel`debugowania ( ) mogą obejmować zarówno poziom, jak i lokalizację.</span><span class="sxs-lookup"><span data-stu-id="2543f-539">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="2543f-540">Poziomy (w kolejności od najmniej do większości pełnych):</span><span class="sxs-lookup"><span data-stu-id="2543f-540">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="2543f-541">BŁĄD</span><span class="sxs-lookup"><span data-stu-id="2543f-541">ERROR</span></span>
* <span data-ttu-id="2543f-542">OSTRZEŻENIE</span><span class="sxs-lookup"><span data-stu-id="2543f-542">WARNING</span></span>
* <span data-ttu-id="2543f-543">Informacji</span><span class="sxs-lookup"><span data-stu-id="2543f-543">INFO</span></span>
* <span data-ttu-id="2543f-544">TRACE</span><span class="sxs-lookup"><span data-stu-id="2543f-544">TRACE</span></span>

<span data-ttu-id="2543f-545">Lokalizacje (dozwolone jest wiele lokalizacji):</span><span class="sxs-lookup"><span data-stu-id="2543f-545">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="2543f-546">Konsoli</span><span class="sxs-lookup"><span data-stu-id="2543f-546">CONSOLE</span></span>
* <span data-ttu-id="2543f-547">Eventlog</span><span class="sxs-lookup"><span data-stu-id="2543f-547">EVENTLOG</span></span>
* <span data-ttu-id="2543f-548">PLIK</span><span class="sxs-lookup"><span data-stu-id="2543f-548">FILE</span></span>

<span data-ttu-id="2543f-549">Ustawienia obsługi mogą być również dostarczane za pośrednictwem zmiennych środowiskowych:</span><span class="sxs-lookup"><span data-stu-id="2543f-549">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="2543f-550">`ASPNETCORE_MODULE_DEBUG_FILE`&ndash; Ścieżka do pliku dziennika debugowania.</span><span class="sxs-lookup"><span data-stu-id="2543f-550">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="2543f-551">(Domyślnie: *aspnetcore-debug.log)*</span><span class="sxs-lookup"><span data-stu-id="2543f-551">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="2543f-552">`ASPNETCORE_MODULE_DEBUG`&ndash; Ustawienie poziomu debugowania.</span><span class="sxs-lookup"><span data-stu-id="2543f-552">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="2543f-553">**Nie** należy pozostawiać rejestrowania debugowania włączone we wdrożeniu dłużej niż wymagane do rozwiązania problemu.</span><span class="sxs-lookup"><span data-stu-id="2543f-553">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="2543f-554">Rozmiar dziennika nie jest ograniczony.</span><span class="sxs-lookup"><span data-stu-id="2543f-554">The size of the log isn't limited.</span></span> <span data-ttu-id="2543f-555">Pozostawienie włączonego dziennika debugowania może wyczerpyć dostępne miejsce na dysku i upaść serwer lub usługę aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-555">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="2543f-556">Zobacz [Konfiguracja z web.config](#configuration-with-webconfig) `aspNetCore` na przykład elementu w pliku *web.config.*</span><span class="sxs-lookup"><span data-stu-id="2543f-556">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="2543f-557">Konfiguracja serwera proxy używa protokołu HTTP i tokenu parowania</span><span class="sxs-lookup"><span data-stu-id="2543f-557">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="2543f-558">*Dotyczy tylko hostingu poza procesem.*</span><span class="sxs-lookup"><span data-stu-id="2543f-558">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="2543f-559">Serwer proxy utworzony między ASP.NET Core Module a Kestrel używa protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="2543f-559">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="2543f-560">Nie ma ryzyka podsłuchiwania ruchu między modułem a Pustułką z lokalizacji poza serwerem.</span><span class="sxs-lookup"><span data-stu-id="2543f-560">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="2543f-561">Token parowania jest używany do zagwarantowania, że żądania odebrane przez Kestrel zostały wysłane przez usługi IIS i nie pochodzą z innego źródła.</span><span class="sxs-lookup"><span data-stu-id="2543f-561">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="2543f-562">Token parowania jest tworzony i ustawiany`ASPNETCORE_TOKEN`w zmiennej środowiskowej ( ) przez moduł.</span><span class="sxs-lookup"><span data-stu-id="2543f-562">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="2543f-563">Token parowania jest również ustawiony`MS-ASPNETCORE-TOKEN`w nagłówku ( ) przy każdym proxied żądania.</span><span class="sxs-lookup"><span data-stu-id="2543f-563">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="2543f-564">Oprogramowanie pośredniczące usługi IIS sprawdza każde otrzymane żądanie, aby potwierdzić, że wartość nagłówka tokenu parowania jest zgodna z wartością zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="2543f-564">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="2543f-565">Jeśli wartości tokenu są niezgodne, żądanie jest rejestrowane i odrzucane.</span><span class="sxs-lookup"><span data-stu-id="2543f-565">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="2543f-566">Zmienna środowiska tokenu parowania i ruch między modułem a Kestrel nie są dostępne z lokalizacji poza serwerem.</span><span class="sxs-lookup"><span data-stu-id="2543f-566">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="2543f-567">Bez znajomości wartości tokenu parowania osoba atakująca nie może przesłać żądań, które omijają sprawdzanie w oprogramowaniu pośredniczącym usługi IIS.</span><span class="sxs-lookup"><span data-stu-id="2543f-567">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="2543f-568">ASP.NET modułu rdzenia z konfiguracją współdzieloną usług IIS</span><span class="sxs-lookup"><span data-stu-id="2543f-568">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="2543f-569">Instalator modułu ASP.NET Core module działa z uprawnieniami konta **TrustedInstaller.**</span><span class="sxs-lookup"><span data-stu-id="2543f-569">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="2543f-570">Ponieważ lokalne konto systemowe nie ma uprawnień do modyfikowania ścieżki udziału używanej przez konfigurację współużytkową usług IIS, instalator zgłasza błąd odmowy dostępu podczas próby skonfigurowania ustawień modułu w pliku *applicationHost.config* w udziale.</span><span class="sxs-lookup"><span data-stu-id="2543f-570">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="2543f-571">W przypadku korzystania z udostępnionej konfiguracji usług IIS na tym samym komputerze co instalacja `OPT_NO_SHARED_CONFIG_CHECK` usług IIS uruchom instalator ASP.NET Core Hosting Bundle z parametrem ustawionym na: `1`</span><span class="sxs-lookup"><span data-stu-id="2543f-571">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="2543f-572">Jeśli ścieżka do konfiguracji udostępnionej nie jest na tym samym komputerze co instalacja usług IIS, wykonaj następujące kroki:</span><span class="sxs-lookup"><span data-stu-id="2543f-572">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="2543f-573">Wyłącz konfigurację udostępnioną usług IIS.</span><span class="sxs-lookup"><span data-stu-id="2543f-573">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="2543f-574">Uruchom instalatora.</span><span class="sxs-lookup"><span data-stu-id="2543f-574">Run the installer.</span></span>
1. <span data-ttu-id="2543f-575">Wyeksportuj zaktualizowany plik *applicationHost.config* do udziału.</span><span class="sxs-lookup"><span data-stu-id="2543f-575">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="2543f-576">Ponownie włącz konfigurację udostępnioną usług IIS.</span><span class="sxs-lookup"><span data-stu-id="2543f-576">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="2543f-577">Dane dotyczące wersji modułu i dzienników instalatora pakietu hostingowego</span><span class="sxs-lookup"><span data-stu-id="2543f-577">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="2543f-578">Aby określić wersję zainstalowanego ASP.NET modułu rdzenia:</span><span class="sxs-lookup"><span data-stu-id="2543f-578">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="2543f-579">W systemie hostingowym przejdź do *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="2543f-579">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="2543f-580">Znajdź plik *aspnetcore.dll.*</span><span class="sxs-lookup"><span data-stu-id="2543f-580">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="2543f-581">Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Właściwości** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="2543f-581">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="2543f-582">Wybierz kartę **Szczegóły.** **Wersja pliku** i **wersja produktu** reprezentują zainstalowaną wersję modułu.</span><span class="sxs-lookup"><span data-stu-id="2543f-582">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="2543f-583">Dzienniki instalatora pakietu hostingowego dla modułu znajdują się pod adresem *C:\\\\Users %UserName%\\AppData\\Local\\Temp*. Plik nosi nazwę *dd_DotNetCoreWinSvrHosting__\<sygnatura czasowa>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="2543f-583">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="2543f-584">Lokalizacje plików modułów, schematów i konfiguracji</span><span class="sxs-lookup"><span data-stu-id="2543f-584">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="2543f-585">Moduł</span><span class="sxs-lookup"><span data-stu-id="2543f-585">Module</span></span>

<span data-ttu-id="2543f-586">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="2543f-586">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="2543f-587">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2543f-587">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="2543f-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2543f-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="2543f-589">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="2543f-589">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="2543f-590">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="2543f-590">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="2543f-591">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="2543f-591">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="2543f-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2543f-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="2543f-593">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2543f-593">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="2543f-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="2543f-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="2543f-595">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="2543f-595">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="2543f-596">Schemat</span><span class="sxs-lookup"><span data-stu-id="2543f-596">Schema</span></span>

<span data-ttu-id="2543f-597">**IIS**</span><span class="sxs-lookup"><span data-stu-id="2543f-597">**IIS**</span></span>

* <span data-ttu-id="2543f-598">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="2543f-598">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="2543f-599">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="2543f-599">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="2543f-600">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="2543f-600">**IIS Express**</span></span>

* <span data-ttu-id="2543f-601">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="2543f-601">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="2543f-602">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="2543f-602">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="2543f-603">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="2543f-603">Configuration</span></span>

<span data-ttu-id="2543f-604">**IIS**</span><span class="sxs-lookup"><span data-stu-id="2543f-604">**IIS**</span></span>

* <span data-ttu-id="2543f-605">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="2543f-605">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="2543f-606">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="2543f-606">**IIS Express**</span></span>

* <span data-ttu-id="2543f-607">Visual Studio: {APPLICATION\\ROOT} .vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="2543f-607">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="2543f-608">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="2543f-608">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="2543f-609">Pliki można znaleźć, wyszukując *aspnetcore* w pliku *applicationHost.config.*</span><span class="sxs-lookup"><span data-stu-id="2543f-609">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="2543f-610">ASP.NET Core Module to natywny moduł usług IIS, który podłącza się do potoku usług IIS w celu przekazywania żądań sieci web do wewnętrznej bazy danych ASP.NET aplikacji Core.</span><span class="sxs-lookup"><span data-stu-id="2543f-610">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="2543f-611">Obsługiwane wersje systemu Windows:</span><span class="sxs-lookup"><span data-stu-id="2543f-611">Supported Windows versions:</span></span>

* <span data-ttu-id="2543f-612">Windows 7 lub nowsze</span><span class="sxs-lookup"><span data-stu-id="2543f-612">Windows 7 or later</span></span>
* <span data-ttu-id="2543f-613">Windows Server 2008 R2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="2543f-613">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="2543f-614">Moduł działa tylko z Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2543f-614">The module only works with Kestrel.</span></span> <span data-ttu-id="2543f-615">Moduł jest niezgodny z [http.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="2543f-615">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="2543f-616">Ponieważ ASP.NET aplikacje Core są uruchamiane w procesie odrębnym od procesu roboczego usług IIS, moduł obsługuje również zarządzanie procesami.</span><span class="sxs-lookup"><span data-stu-id="2543f-616">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="2543f-617">Moduł uruchamia proces aplikacji ASP.NET Core po nadejściu pierwszego żądania i uruchamia ponownie aplikację w przypadku awarii.</span><span class="sxs-lookup"><span data-stu-id="2543f-617">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="2543f-618">Jest to zasadniczo to samo zachowanie, jak w ASP.NET aplikacje 4.x, które działają w procesie w usługach IIS, które są zarządzane przez [usługę aktywacji procesów systemu Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="2543f-618">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="2543f-619">Na poniższym diagramie przedstawiono relację między iis, ASP.NET Core Module i aplikacji:</span><span class="sxs-lookup"><span data-stu-id="2543f-619">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Moduł ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="2543f-621">Żądania przychodzą z sieci Web do sterownika HTTP.sys w trybie jądra.</span><span class="sxs-lookup"><span data-stu-id="2543f-621">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="2543f-622">Sterownik kieruje żądania do iIS w skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2543f-622">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="2543f-623">Moduł przekazuje żądania do Kestrel na losowym porcie dla aplikacji, który nie jest portem 80 lub 443.</span><span class="sxs-lookup"><span data-stu-id="2543f-623">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="2543f-624">Moduł określa port za pośrednictwem zmiennej środowiskowej podczas uruchamiania, a [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) konfiguruje serwer do nasłuchiwać na `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="2543f-624">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="2543f-625">Dodatkowe kontrole są wykonywane, a żądania, które nie pochodzą z modułu są odrzucane.</span><span class="sxs-lookup"><span data-stu-id="2543f-625">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="2543f-626">Moduł nie obsługuje przekazywania protokołu HTTPS, więc żądania są przekazywane za pośrednictwem protokołu HTTP, nawet jeśli są odbierane przez usługi IIS za pośrednictwem protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2543f-626">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="2543f-627">Po Kestrel odbiera żądanie z modułu, żądanie jest wypychane do potoku oprogramowania pośredniczącego ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2543f-627">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="2543f-628">Potok oprogramowania pośredniczącego obsługuje żądanie i `HttpContext` przekazuje go jako wystąpienie do logiki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-628">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="2543f-629">Oprogramowanie pośredniczące dodane przez integrację usług IIS aktualizuje schemat, zdalny adres IP i bazę ścieżek w celu przekazania żądania do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2543f-629">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="2543f-630">Odpowiedź aplikacji jest przekazywana z powrotem do iIS, co wypycha ją z powrotem do klienta HTTP, który zainicjował żądanie.</span><span class="sxs-lookup"><span data-stu-id="2543f-630">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="2543f-631">Wiele modułów natywnych, takich jak uwierzytelnianie systemu Windows, pozostaje aktywnych.</span><span class="sxs-lookup"><span data-stu-id="2543f-631">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="2543f-632">Aby dowiedzieć się więcej o modułach usług IIS <xref:host-and-deploy/iis/modules>aktywnych za pomocą ASP.NET Core Module, zobacz .</span><span class="sxs-lookup"><span data-stu-id="2543f-632">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="2543f-633">ASP.NET Moduł podstawowy może również:</span><span class="sxs-lookup"><span data-stu-id="2543f-633">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="2543f-634">Ustawianie zmiennych środowiskowych dla procesu roboczego.</span><span class="sxs-lookup"><span data-stu-id="2543f-634">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="2543f-635">Dane wyjściowe stdout do magazynu plików w celu rozwiązywania problemów z uruchamianiem.</span><span class="sxs-lookup"><span data-stu-id="2543f-635">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="2543f-636">Przesyłanie dalej tokenów uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="2543f-636">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="2543f-637">Jak zainstalować i korzystać z modułu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2543f-637">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="2543f-638">Aby uzyskać instrukcje dotyczące instalowania modułu ASP.NET Core, zobacz [Instalowanie pakietu hostingowego .NET Core .](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)</span><span class="sxs-lookup"><span data-stu-id="2543f-638">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="2543f-639">Konfiguracja z web.config</span><span class="sxs-lookup"><span data-stu-id="2543f-639">Configuration with web.config</span></span>

<span data-ttu-id="2543f-640">Moduł ASP.NET Core jest skonfigurowany z `aspNetCore` sekcją `system.webServer` węzła w pliku *web.config* witryny.</span><span class="sxs-lookup"><span data-stu-id="2543f-640">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="2543f-641">Następujący plik *web.config* jest publikowany dla [wdrożenia zależnego od struktury](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) i konfiguruje ASP.NET Core Module do obsługi żądań lokacji:</span><span class="sxs-lookup"><span data-stu-id="2543f-641">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="2543f-642">Następujące *web.config* jest publikowany dla [samodzielnego wdrożenia:](/dotnet/articles/core/deploying/#self-contained-deployments-scd)</span><span class="sxs-lookup"><span data-stu-id="2543f-642">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="2543f-643">Gdy aplikacja jest wdrażana w [usłudze Azure App Service,](https://azure.microsoft.com/services/app-service/)ścieżka jest ustawiona `stdoutLogFile` na `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="2543f-643">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="2543f-644">Ścieżka zapisuje dzienniki stdout do folderu *LogFiles,* który jest lokalizacją automatycznie utworzoną przez usługę.</span><span class="sxs-lookup"><span data-stu-id="2543f-644">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="2543f-645">Aby uzyskać informacje na temat konfiguracji <xref:host-and-deploy/iis/index#sub-applications>podpołowieczek usług IIS, zobacz .</span><span class="sxs-lookup"><span data-stu-id="2543f-645">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="2543f-646">Atrybuty elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="2543f-646">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="2543f-647">Atrybut</span><span class="sxs-lookup"><span data-stu-id="2543f-647">Attribute</span></span> | <span data-ttu-id="2543f-648">Opis</span><span class="sxs-lookup"><span data-stu-id="2543f-648">Description</span></span> | <span data-ttu-id="2543f-649">Domyślne</span><span class="sxs-lookup"><span data-stu-id="2543f-649">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="2543f-650">Opcjonalny atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="2543f-650">Optional string attribute.</span></span></p><p><span data-ttu-id="2543f-651">Argumenty do pliku wykonywalnego określonego w **processPath**.</span><span class="sxs-lookup"><span data-stu-id="2543f-651">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="2543f-652">Opcjonalny atrybut logiczny.</span><span class="sxs-lookup"><span data-stu-id="2543f-652">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2543f-653">Jeśli true, **502.5 - Błąd procesu** strona jest pomijana, a strona kod stanu 502 skonfigurowana w *web.config* ma pierwszeństwo.</span><span class="sxs-lookup"><span data-stu-id="2543f-653">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="2543f-654">Opcjonalny atrybut logiczny.</span><span class="sxs-lookup"><span data-stu-id="2543f-654">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2543f-655">Jeśli true, token jest przekazywanie dalej do procesu podrzędnego nasłuchiwania na %ASPNETCORE_PORT% jako nagłówek "MS-ASPNETCORE-WINAUTHTOKEN" na żądanie.</span><span class="sxs-lookup"><span data-stu-id="2543f-655">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="2543f-656">Jest odpowiedzialny za tego procesu do wywołania CloseHandle na ten token na żądanie.</span><span class="sxs-lookup"><span data-stu-id="2543f-656">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="2543f-657">Opcjonalny atrybut liczby całkowitej.</span><span class="sxs-lookup"><span data-stu-id="2543f-657">Optional integer attribute.</span></span></p><p><span data-ttu-id="2543f-658">Określa liczbę wystąpień procesu określonego w **ustawieniu processPath,** które można obrócić na aplikację.</span><span class="sxs-lookup"><span data-stu-id="2543f-658">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="2543f-659">Ustawienie `processesPerApplication` jest odradzane.</span><span class="sxs-lookup"><span data-stu-id="2543f-659">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="2543f-660">Ten atrybut zostanie usunięty w przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="2543f-660">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="2543f-661">Domyślny:`1`</span><span class="sxs-lookup"><span data-stu-id="2543f-661">Default: `1`</span></span><br><span data-ttu-id="2543f-662">Min:`1`</span><span class="sxs-lookup"><span data-stu-id="2543f-662">Min: `1`</span></span><br><span data-ttu-id="2543f-663">Max:`100`</span><span class="sxs-lookup"><span data-stu-id="2543f-663">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="2543f-664">Wymagany atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="2543f-664">Required string attribute.</span></span></p><p><span data-ttu-id="2543f-665">Ścieżka do pliku wykonywalnego, który uruchamia proces nasłuchiwania żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="2543f-665">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="2543f-666">Ścieżki względne są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="2543f-666">Relative paths are supported.</span></span> <span data-ttu-id="2543f-667">Jeśli ścieżka zaczyna `.`się od , ścieżka jest uważana za względem względem katalogu głównego witryny.</span><span class="sxs-lookup"><span data-stu-id="2543f-667">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="2543f-668">Opcjonalny atrybut liczby całkowitej.</span><span class="sxs-lookup"><span data-stu-id="2543f-668">Optional integer attribute.</span></span></p><p><span data-ttu-id="2543f-669">Określa, ile razy proces określony w **processPath** może ulec awarii na minutę.</span><span class="sxs-lookup"><span data-stu-id="2543f-669">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="2543f-670">Jeśli ten limit zostanie przekroczony, moduł przestaje uruchamiać proces przez pozostałą część minuty.</span><span class="sxs-lookup"><span data-stu-id="2543f-670">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="2543f-671">Domyślny:`10`</span><span class="sxs-lookup"><span data-stu-id="2543f-671">Default: `10`</span></span><br><span data-ttu-id="2543f-672">Min:`0`</span><span class="sxs-lookup"><span data-stu-id="2543f-672">Min: `0`</span></span><br><span data-ttu-id="2543f-673">Max:`100`</span><span class="sxs-lookup"><span data-stu-id="2543f-673">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="2543f-674">Opcjonalny atrybut przedziału czasu.</span><span class="sxs-lookup"><span data-stu-id="2543f-674">Optional timespan attribute.</span></span></p><p><span data-ttu-id="2543f-675">Określa czas trwania oczekiwania modułu ASP.NET Core module na odpowiedź z procesu nasłuchiwania na %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="2543f-675">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="2543f-676">W wersjach modułu ASP.NET Core, który został dostarczony wraz z wydaniem ASP.NET Core 2.1 lub nowszym, `requestTimeout` jest określony w godzinach, minutach i sekundach.</span><span class="sxs-lookup"><span data-stu-id="2543f-676">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="2543f-677">Domyślny:`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="2543f-677">Default: `00:02:00`</span></span><br><span data-ttu-id="2543f-678">Min:`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="2543f-678">Min: `00:00:00`</span></span><br><span data-ttu-id="2543f-679">Max:`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="2543f-679">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="2543f-680">Opcjonalny atrybut liczby całkowitej.</span><span class="sxs-lookup"><span data-stu-id="2543f-680">Optional integer attribute.</span></span></p><p><span data-ttu-id="2543f-681">Czas trwania w sekundach, że moduł czeka na plik wykonywalny bezpiecznie zamknąć po wykryciu *pliku app_offline.htm.*</span><span class="sxs-lookup"><span data-stu-id="2543f-681">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="2543f-682">Domyślny:`10`</span><span class="sxs-lookup"><span data-stu-id="2543f-682">Default: `10`</span></span><br><span data-ttu-id="2543f-683">Min:`0`</span><span class="sxs-lookup"><span data-stu-id="2543f-683">Min: `0`</span></span><br><span data-ttu-id="2543f-684">Max:`600`</span><span class="sxs-lookup"><span data-stu-id="2543f-684">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="2543f-685">Opcjonalny atrybut liczby całkowitej.</span><span class="sxs-lookup"><span data-stu-id="2543f-685">Optional integer attribute.</span></span></p><p><span data-ttu-id="2543f-686">Czas trwania w sekundach, że moduł czeka na plik wykonywalny, aby rozpocząć proces nasłuchiwania na porcie.</span><span class="sxs-lookup"><span data-stu-id="2543f-686">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="2543f-687">Jeśli ten limit czasu zostanie przekroczony, moduł zabija proces.</span><span class="sxs-lookup"><span data-stu-id="2543f-687">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="2543f-688">Moduł próbuje ponownie uruchomić proces po otrzymaniu nowego żądania i kontynuuje próbę ponownego uruchomienia procesu na kolejne żądania przychodzące, chyba że aplikacja nie może uruchomić **rapidFailsPerMinute** liczba razy w ostatniej toczenia minut.</span><span class="sxs-lookup"><span data-stu-id="2543f-688">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="2543f-689">Wartość 0 (zero) **nie** jest uważany za nieskończony limit czasu.</span><span class="sxs-lookup"><span data-stu-id="2543f-689">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="2543f-690">Domyślny:`120`</span><span class="sxs-lookup"><span data-stu-id="2543f-690">Default: `120`</span></span><br><span data-ttu-id="2543f-691">Min:`0`</span><span class="sxs-lookup"><span data-stu-id="2543f-691">Min: `0`</span></span><br><span data-ttu-id="2543f-692">Max:`3600`</span><span class="sxs-lookup"><span data-stu-id="2543f-692">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="2543f-693">Opcjonalny atrybut logiczny.</span><span class="sxs-lookup"><span data-stu-id="2543f-693">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2543f-694">Jeśli true, **stdout** i **stderr** dla procesu określonego w **processPath** są przekierowywane do pliku określonego w **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="2543f-694">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="2543f-695">Opcjonalny atrybut ciągu.</span><span class="sxs-lookup"><span data-stu-id="2543f-695">Optional string attribute.</span></span></p><p><span data-ttu-id="2543f-696">Określa względną lub bezwzględną ścieżkę pliku, dla której są rejestrowane **stdout** i **stderr** z procesu określonego w **processPath.**</span><span class="sxs-lookup"><span data-stu-id="2543f-696">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="2543f-697">Ścieżki względne są względem katalogu głównego witryny.</span><span class="sxs-lookup"><span data-stu-id="2543f-697">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="2543f-698">Każda ścieżka `.` rozpoczynająca się względem katalogu głównego witryny i wszystkie inne ścieżki są traktowane jako ścieżki bezwzględne.</span><span class="sxs-lookup"><span data-stu-id="2543f-698">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="2543f-699">Wszystkie foldery podane w ścieżce muszą istnieć, aby moduł utworzył plik dziennika.</span><span class="sxs-lookup"><span data-stu-id="2543f-699">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="2543f-700">Za pomocą ograniczników podkreślenia do ostatniego segmentu ścieżki **pliku stdoutLogFile** dodaje się znacznik czasu, identyfikator procesu i rozszerzenie pliku (*.log).*</span><span class="sxs-lookup"><span data-stu-id="2543f-700">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="2543f-701">Jeśli `.\logs\stdout` jest dostarczany jako wartość, przykładowy dziennik stdout jest zapisywany jako *stdout_20180205194132_1934.log* w folderze *dzienników* po zapisaniu w 2/5/2018 o godzinie 19:41:32 z identyfikatorem procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="2543f-701">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="2543f-702">Ustawianie zmiennych środowiskowych</span><span class="sxs-lookup"><span data-stu-id="2543f-702">Setting environment variables</span></span>

<span data-ttu-id="2543f-703">Zmienne środowiskowe można określić `processPath` dla procesu w atrybucie.</span><span class="sxs-lookup"><span data-stu-id="2543f-703">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="2543f-704">Określ zmienną `<environmentVariable>` środowiskową z `<environmentVariables>` elementem podrzędnym elementu kolekcji.</span><span class="sxs-lookup"><span data-stu-id="2543f-704">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="2543f-705">Zmienne środowiskowe ustawione w tej sekcji są w konflikcie ze zmiennymi środowiska systemowego ustawionymi o tej samej nazwie.</span><span class="sxs-lookup"><span data-stu-id="2543f-705">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="2543f-706">Jeśli zmienna środowiskowa jest ustawiona zarówno w pliku *web.config,* jak i na poziomie systemu w systemie Windows, wartość z `ASPNETCORE_ENVIRONMENT: Development;Development`pliku *web.config* zostanie dołączona do wartości zmiennej środowiska systemowego (na przykład ), która uniemożliwia uruchomienie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-706">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="2543f-707">W poniższym przykładzie ustawia dwie zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="2543f-707">The following example sets two environment variables.</span></span> <span data-ttu-id="2543f-708">`ASPNETCORE_ENVIRONMENT`konfiguruje środowisko aplikacji `Development`na .</span><span class="sxs-lookup"><span data-stu-id="2543f-708">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="2543f-709">Deweloper może tymczasowo ustawić tę wartość w pliku *web.config,* aby wymusić na [stronie wyjątku dewelopera](xref:fundamentals/error-handling) załadować podczas debugowania wyjątku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-709">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="2543f-710">`CONFIG_DIR`jest przykładem zmiennej środowiskowej zdefiniowanej przez użytkownika, gdzie deweloper napisał kod, który odczytuje wartość podczas uruchamiania, tworząc ścieżkę do ładowania pliku konfiguracyjnego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-710">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="2543f-711">Zmienną `ASPNETCORE_ENVIRONMENT` środowiskową `Development` należy ustawić tylko na serwery przejściowe i testujące, które nie są dostępne dla niezaufanych sieci, takich jak Internet.</span><span class="sxs-lookup"><span data-stu-id="2543f-711">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="2543f-712">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="2543f-712">app_offline.htm</span></span>

<span data-ttu-id="2543f-713">Jeśli plik o nazwie *app_offline.htm* zostanie wykryty w katalogu głównym aplikacji, moduł ASP.NET Core próbuje bezpiecznie zamknąć aplikację i zatrzymać przetwarzanie żądań przychodzących.</span><span class="sxs-lookup"><span data-stu-id="2543f-713">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="2543f-714">Jeśli aplikacja jest nadal uruchomiona po liczbie `shutdownTimeLimit`sekund zdefiniowanych w , ASP.NET moduł podstawowy zabija uruchomiony proces.</span><span class="sxs-lookup"><span data-stu-id="2543f-714">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="2543f-715">Podczas gdy plik *app_offline.htm* jest obecny, ASP.NET Core Module odpowiada na żądania, wysyłając z powrotem zawartość pliku *app_offline.htm.*</span><span class="sxs-lookup"><span data-stu-id="2543f-715">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="2543f-716">Po usunięciu pliku *app_offline.htm* następne żądanie uruchamia aplikację.</span><span class="sxs-lookup"><span data-stu-id="2543f-716">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="2543f-717">Strona błędu uruchamiania</span><span class="sxs-lookup"><span data-stu-id="2543f-717">Start-up error page</span></span>

<span data-ttu-id="2543f-718">Jeśli ASP.NET modułu rdzenia nie może uruchomić procesu wewnętrznej bazy danych lub rozpocznie się proces wewnętrznej bazy danych, ale nie nasłuchuje na skonfigurowanym porcie, zostanie wyświetlona strona kodowa stanu *awarii procesu 502.5.*</span><span class="sxs-lookup"><span data-stu-id="2543f-718">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="2543f-719">Aby pominąć tę stronę i przywrócić domyślną stronę kodową stanu `disableStartUpErrorPage` IIS 502, użyj tego atrybutu.</span><span class="sxs-lookup"><span data-stu-id="2543f-719">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="2543f-720">Aby uzyskać więcej informacji na temat konfigurowania niestandardowych komunikatów o [błędach, zobacz Błędy \<HTTP httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="2543f-720">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![502.5 Strona kodowa stanu awarii procesu](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="2543f-722">Tworzenie i przekierowywanie w dzienniku</span><span class="sxs-lookup"><span data-stu-id="2543f-722">Log creation and redirection</span></span>

<span data-ttu-id="2543f-723">Moduł ASP.NET Core przekierowuje wyjście konsoli stdout i stderr na dysk, `stdoutLogEnabled` jeśli ustawiono atrybuty i `stdoutLogFile` atrybuty `aspNetCore` elementu.</span><span class="sxs-lookup"><span data-stu-id="2543f-723">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="2543f-724">Wszystkie foldery `stdoutLogFile` w ścieżce są tworzone przez moduł podczas tworzenia pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="2543f-724">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="2543f-725">Pula aplikacji musi mieć dostęp do zapisu do lokalizacji, w której są zapisywane dzienniki (służy `IIS AppPool\<app_pool_name>` do udzielania uprawnień do zapisu).</span><span class="sxs-lookup"><span data-stu-id="2543f-725">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="2543f-726">Dzienniki nie są obracane, chyba że proces recyklingu /ponownego uruchamiania występuje.</span><span class="sxs-lookup"><span data-stu-id="2543f-726">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="2543f-727">Jest odpowiedzialny za hosta, aby ograniczyć miejsce na dysku dzienniki zużywają.</span><span class="sxs-lookup"><span data-stu-id="2543f-727">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="2543f-728">Używanie dziennika stdout jest zalecane tylko do rozwiązywania problemów z uruchamianiem aplikacji podczas hostingu w serwisie IIS lub podczas korzystania [z obsługi programów IIS w czasie tworzenia w programie Visual Studio,](xref:host-and-deploy/iis/development-time-iis-support)a nie podczas debugowania lokalnie i uruchamiania aplikacji za pomocą usługi IIS Express.</span><span class="sxs-lookup"><span data-stu-id="2543f-728">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="2543f-729">Nie używaj dziennika stdout do ogólnych celów rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2543f-729">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="2543f-730">W przypadku rutynowego rejestrowania w aplikacji ASP.NET Core należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki.</span><span class="sxs-lookup"><span data-stu-id="2543f-730">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2543f-731">Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych firm](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="2543f-731">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="2543f-732">Sygnatura czasowa i rozszerzenie pliku są dodawane automatycznie podczas tworzenia pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="2543f-732">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="2543f-733">Nazwa pliku dziennika składa się przez dołączenie sygnatury czasowej, identyfikatora procesu i `stdoutLogFile` rozszerzenia pliku *(.log*) do ostatniego segmentu ścieżki (zazwyczaj *stdout)* rozdzielonego przez podkreślenia.</span><span class="sxs-lookup"><span data-stu-id="2543f-733">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="2543f-734">Jeśli `stdoutLogFile` ścieżka kończy się *stdout*, dziennik dla aplikacji z PID 1934 utworzony w dniu 2/5/2018 o 19:42:32 ma nazwę pliku *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="2543f-734">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="2543f-735">Poniższy `aspNetCore` przykładowy element konfiguruje rejestrowanie `.\log\`stdout w ścieżce względnej .</span><span class="sxs-lookup"><span data-stu-id="2543f-735">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="2543f-736">Upewnij się, że tożsamość użytkownika AppPool ma uprawnienia do zapisu w podanej ścieżce.</span><span class="sxs-lookup"><span data-stu-id="2543f-736">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="2543f-737">Podczas publikowania aplikacji dla wdrożenia usługi Azure App Service `stdoutLogFile` zestaw `\\?\%home%\LogFiles\stdout`SDK sieci Web ustawia wartość .</span><span class="sxs-lookup"><span data-stu-id="2543f-737">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="2543f-738">Zmienna środowiskowa `%home` jest wstępnie zdefiniowana dla aplikacji obsługiwanych przez usługę Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="2543f-738">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="2543f-739">Aby utworzyć reguły filtrowania rejestrowania, zobacz sekcje [filtrowania](xref:fundamentals/logging/index#log-filtering) [konfiguracji](xref:fundamentals/logging/index#log-filtering) i dziennika w dokumentacji rejestrowania ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2543f-739">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="2543f-740">Aby uzyskać więcej informacji na temat formatów ścieżek, zobacz [Formaty ścieżek plików w systemach Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="2543f-740">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="2543f-741">Konfiguracja serwera proxy używa protokołu HTTP i tokenu parowania</span><span class="sxs-lookup"><span data-stu-id="2543f-741">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="2543f-742">Serwer proxy utworzony między ASP.NET Core Module a Kestrel używa protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="2543f-742">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="2543f-743">Nie ma ryzyka podsłuchiwania ruchu między modułem a Pustułką z lokalizacji poza serwerem.</span><span class="sxs-lookup"><span data-stu-id="2543f-743">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="2543f-744">Token parowania jest używany do zagwarantowania, że żądania odebrane przez Kestrel zostały wysłane przez usługi IIS i nie pochodzą z innego źródła.</span><span class="sxs-lookup"><span data-stu-id="2543f-744">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="2543f-745">Token parowania jest tworzony i ustawiany`ASPNETCORE_TOKEN`w zmiennej środowiskowej ( ) przez moduł.</span><span class="sxs-lookup"><span data-stu-id="2543f-745">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="2543f-746">Token parowania jest również ustawiony`MS-ASPNETCORE-TOKEN`w nagłówku ( ) przy każdym proxied żądania.</span><span class="sxs-lookup"><span data-stu-id="2543f-746">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="2543f-747">Oprogramowanie pośredniczące usługi IIS sprawdza każde otrzymane żądanie, aby potwierdzić, że wartość nagłówka tokenu parowania jest zgodna z wartością zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="2543f-747">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="2543f-748">Jeśli wartości tokenu są niezgodne, żądanie jest rejestrowane i odrzucane.</span><span class="sxs-lookup"><span data-stu-id="2543f-748">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="2543f-749">Zmienna środowiska tokenu parowania i ruch między modułem a Kestrel nie są dostępne z lokalizacji poza serwerem.</span><span class="sxs-lookup"><span data-stu-id="2543f-749">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="2543f-750">Bez znajomości wartości tokenu parowania osoba atakująca nie może przesłać żądań, które omijają sprawdzanie w oprogramowaniu pośredniczącym usługi IIS.</span><span class="sxs-lookup"><span data-stu-id="2543f-750">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="2543f-751">ASP.NET modułu rdzenia z konfiguracją współdzieloną usług IIS</span><span class="sxs-lookup"><span data-stu-id="2543f-751">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="2543f-752">Instalator modułu ASP.NET Core module działa z uprawnieniami konta **TrustedInstaller.**</span><span class="sxs-lookup"><span data-stu-id="2543f-752">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="2543f-753">Ponieważ lokalne konto systemowe nie ma uprawnień do modyfikowania ścieżki udziału używanej przez konfigurację współużytkową usług IIS, instalator zgłasza błąd odmowy dostępu podczas próby skonfigurowania ustawień modułu w pliku *applicationHost.config* w udziale.</span><span class="sxs-lookup"><span data-stu-id="2543f-753">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="2543f-754">Korzystając z konfiguracji udostępnionej usług IIS, wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="2543f-754">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="2543f-755">Wyłącz konfigurację udostępnioną usług IIS.</span><span class="sxs-lookup"><span data-stu-id="2543f-755">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="2543f-756">Uruchom instalatora.</span><span class="sxs-lookup"><span data-stu-id="2543f-756">Run the installer.</span></span>
1. <span data-ttu-id="2543f-757">Wyeksportuj zaktualizowany plik *applicationHost.config* do udziału.</span><span class="sxs-lookup"><span data-stu-id="2543f-757">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="2543f-758">Ponownie włącz konfigurację udostępnioną usług IIS.</span><span class="sxs-lookup"><span data-stu-id="2543f-758">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="2543f-759">Dane dotyczące wersji modułu i dzienników instalatora pakietu hostingowego</span><span class="sxs-lookup"><span data-stu-id="2543f-759">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="2543f-760">Aby określić wersję zainstalowanego ASP.NET modułu rdzenia:</span><span class="sxs-lookup"><span data-stu-id="2543f-760">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="2543f-761">W systemie hostingowym przejdź do *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="2543f-761">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="2543f-762">Znajdź plik *aspnetcore.dll.*</span><span class="sxs-lookup"><span data-stu-id="2543f-762">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="2543f-763">Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Właściwości** z menu kontekstowego.</span><span class="sxs-lookup"><span data-stu-id="2543f-763">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="2543f-764">Wybierz kartę **Szczegóły.** **Wersja pliku** i **wersja produktu** reprezentują zainstalowaną wersję modułu.</span><span class="sxs-lookup"><span data-stu-id="2543f-764">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="2543f-765">Dzienniki instalatora pakietu hostingowego dla modułu znajdują się pod adresem *C:\\\\Users %UserName%\\AppData\\Local\\Temp*. Plik nosi nazwę *dd_DotNetCoreWinSvrHosting__\<sygnatura czasowa>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="2543f-765">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="2543f-766">Lokalizacje plików modułów, schematów i konfiguracji</span><span class="sxs-lookup"><span data-stu-id="2543f-766">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="2543f-767">Moduł</span><span class="sxs-lookup"><span data-stu-id="2543f-767">Module</span></span>

<span data-ttu-id="2543f-768">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="2543f-768">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="2543f-769">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2543f-769">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="2543f-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2543f-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="2543f-771">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="2543f-771">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="2543f-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2543f-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="2543f-773">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2543f-773">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="2543f-774">Schemat</span><span class="sxs-lookup"><span data-stu-id="2543f-774">Schema</span></span>

<span data-ttu-id="2543f-775">**IIS**</span><span class="sxs-lookup"><span data-stu-id="2543f-775">**IIS**</span></span>

* <span data-ttu-id="2543f-776">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="2543f-776">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="2543f-777">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="2543f-777">**IIS Express**</span></span>

* <span data-ttu-id="2543f-778">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="2543f-778">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="2543f-779">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="2543f-779">Configuration</span></span>

<span data-ttu-id="2543f-780">**IIS**</span><span class="sxs-lookup"><span data-stu-id="2543f-780">**IIS**</span></span>

* <span data-ttu-id="2543f-781">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="2543f-781">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="2543f-782">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="2543f-782">**IIS Express**</span></span>

* <span data-ttu-id="2543f-783">Visual Studio: {APPLICATION\\ROOT} .vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="2543f-783">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="2543f-784">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="2543f-784">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="2543f-785">Pliki można znaleźć, wyszukując *aspnetcore* w pliku *applicationHost.config.*</span><span class="sxs-lookup"><span data-stu-id="2543f-785">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="2543f-786">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="2543f-786">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="2543f-787">[ASP.NET źródło referencyjne modułu rdzenia (gałąź główna)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2) &ndash; Użyj listy rozwijanej **Gałąź,** `release/3.1`aby wybrać określoną wersję (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="2543f-787">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2) &ndash; Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
