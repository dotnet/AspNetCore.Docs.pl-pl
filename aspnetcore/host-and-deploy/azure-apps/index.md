---
title: Wdrażanie aplikacji ASP.NET Core w usłudze Azure App Service
author: bradygaster
description: Ten artykuł zawiera łącza do hosta platformy Azure i wdrażania zasobów.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/16/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: ba9671f68a0faf99ff5232a6d5dd132d0a1d5ac5
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665145"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a><span data-ttu-id="b0e6d-103">Wdrażanie aplikacji ASP.NET Core w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b0e6d-103">Deploy ASP.NET Core apps to Azure App Service</span></span>

<span data-ttu-id="b0e6d-104">[Usługa Azure App Service](https://azure.microsoft.com/services/app-service/) to [usługa platformy przetwarzania w chmurze firmy Microsoft](https://azure.microsoft.com/) do obsługi aplikacji sieci Web, w tym ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span>

## <a name="useful-resources"></a><span data-ttu-id="b0e6d-105">Przydatne zasoby</span><span class="sxs-lookup"><span data-stu-id="b0e6d-105">Useful resources</span></span>

<span data-ttu-id="b0e6d-106">[Dokumentacja usługi app service](/azure/app-service/) jest domem dla usługi Azure Apps dokumentacji, samouczki, przykłady, poradniki i inne zasoby.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-106">[App Service Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span></span> <span data-ttu-id="b0e6d-107">Dwa godne uwagi samouczki dotyczące hostingu ASP.NET aplikacji Core to:</span><span class="sxs-lookup"><span data-stu-id="b0e6d-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span></span>

[<span data-ttu-id="b0e6d-108">Tworzenie aplikacji internetowej ASP.NET Core na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="b0e6d-108">Create an ASP.NET Core web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)  
<span data-ttu-id="b0e6d-109">Program Visual Studio służy do tworzenia i wdrażania aplikacji sieci web ASP.NET Core w usłudze Azure App Service w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span></span>

[<span data-ttu-id="b0e6d-110">Tworzenie aplikacji platformy ASP.NET Core w usłudze App Service dla systemu Linux</span><span class="sxs-lookup"><span data-stu-id="b0e6d-110">Create an ASP.NET Core app in App Service on Linux</span></span>](/azure/app-service/containers/quickstart-dotnetcore)  
<span data-ttu-id="b0e6d-111">Użyj wiersza polecenia, aby utworzyć i wdrożyć ASP.NET podstawową aplikację sieci web w usłudze Azure App Service w systemie Linux.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span></span>

<span data-ttu-id="b0e6d-112">Zobacz [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) dla wersji ASP.NET Core dostępnej w usłudze Azure App.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-112">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

<span data-ttu-id="b0e6d-113">Subskrybuj repozytorium [anonse usługi app service](https://github.com/Azure/app-service-announcements/) i monitoruj problemy.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-113">Subscribe to the [App Service Announcements](https://github.com/Azure/app-service-announcements/) repository and monitor the issues.</span></span> <span data-ttu-id="b0e6d-114">Zespół usługi app service regularnie publikuje anonse i scenariusze przychodzące do usługi App Service.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-114">The App Service team regularly posts announcements and scenarios arriving in App Service.</span></span>

<span data-ttu-id="b0e6d-115">Następujące artykuły są dostępne w ASP.NET dokumentacji Core:</span><span class="sxs-lookup"><span data-stu-id="b0e6d-115">The following articles are available in ASP.NET Core documentation:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="b0e6d-116">Dowiedz się, jak opublikować aplikację ASP.NET Core w usłudze Azure App Service za pomocą programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-116">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
<span data-ttu-id="b0e6d-117">Dowiedz się, jak utworzyć aplikację sieci web ASP.NET Core za pomocą programu Visual Studio i wdrożyć ją w usłudze Azure App Service przy użyciu git do ciągłego wdrażania.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-117">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span></span>

[<span data-ttu-id="b0e6d-118">Tworzenie pierwszego potoku</span><span class="sxs-lookup"><span data-stu-id="b0e6d-118">Create your first pipeline</span></span>](/azure/devops/pipelines/get-started-yaml)  
<span data-ttu-id="b0e6d-119">Skonfiguruj kompilację ciągłej integracji dla aplikacji ASP.NET Core, a następnie utwórz wydanie ciągłego wdrażania usługi Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-119">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span></span>

[<span data-ttu-id="b0e6d-120">Piaskownica aplikacji Azure Web App</span><span class="sxs-lookup"><span data-stu-id="b0e6d-120">Azure Web App sandbox</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
<span data-ttu-id="b0e6d-121">Odkryj ograniczenia wykonywania usługi Azure App Service wymuszane przez platformę Azure Apps.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-121">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span></span>

<xref:test/troubleshoot>  
<span data-ttu-id="b0e6d-122">Zrozumienie i rozwiązywanie problemów z ostrzeżeniami i błędami w projektach ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-122">Understand and troubleshoot warnings and errors with ASP.NET Core projects.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="b0e6d-123">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="b0e6d-123">Application configuration</span></span>

### <a name="platform"></a><span data-ttu-id="b0e6d-124">Platforma</span><span class="sxs-lookup"><span data-stu-id="b0e6d-124">Platform</span></span>

<span data-ttu-id="b0e6d-125">Architektura platformy (x86/x64) aplikacji usługi App Services jest ustawiona w ustawieniach aplikacji w witrynie Azure Portal dla aplikacji, które są hostowane w warstwie obliczeniowej serii A (Basic) lub wyższej.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-125">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="b0e6d-126">Upewnij się, że ustawienia publikowania aplikacji (na przykład w profilu publikowania w programie Visual Studio [(pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) są zgodne z ustawieniem w konfiguracji usługi aplikacji w witrynie Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-126">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure Portal.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b0e6d-127">Środowiska wykonawcze dla aplikacji 64-bitowych (x64) i 32-bitowych (x86) są obecne w usłudze Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-127">Runtimes for 64-bit (x64) and 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="b0e6d-128">Pakiet [.NET Core SDK](/dotnet/core/sdk) dostępny w usłudze App Service jest 32-bitowy, ale można wdrażać aplikacje 64-bitowe utworzone lokalnie przy użyciu konsoli [Kudu](https://github.com/projectkudu/kudu/wiki) lub procesu publikowania w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-128">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit, but you can deploy 64-bit apps built locally using the [Kudu](https://github.com/projectkudu/kudu/wiki) console or the publish process in Visual Studio.</span></span> <span data-ttu-id="b0e6d-129">Aby uzyskać więcej informacji, zobacz [publikowanie i wdrażanie](#publish-and-deploy-the-app) sekcji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-129">For more information, see the [Publish and deploy the app](#publish-and-deploy-the-app) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b0e6d-130">W przypadku aplikacji z natywnymi zależnościami środowiska wykonawcze dla aplikacji 32-bitowych (x86) są obecne w usłudze Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-130">For apps with native dependencies, runtimes for 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="b0e6d-131">Plik [SDK .NET Core](/dotnet/core/sdk) dostępny w usłudze App Service jest 32-bitowy.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-131">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit.</span></span>

::: moniker-end

<span data-ttu-id="b0e6d-132">Aby uzyskać więcej informacji na temat składników frameworka .NET Core i metod dystrybucji, takich jak informacje o czasie wykonywania .NET Core i .NET Core SDK, zobacz [Informacje o rdzeniu net: skład](/dotnet/core/about#composition).</span><span class="sxs-lookup"><span data-stu-id="b0e6d-132">For more information on .NET Core framework components and distribution methods, such as information on the .NET Core runtime and the .NET Core SDK, see [About .NET Core: Composition](/dotnet/core/about#composition).</span></span>

### <a name="packages"></a><span data-ttu-id="b0e6d-133">Pakiety</span><span class="sxs-lookup"><span data-stu-id="b0e6d-133">Packages</span></span>

<span data-ttu-id="b0e6d-134">Dołącz następujące pakiety NuGet, aby zapewnić funkcje automatycznego rejestrowania dla aplikacji wdrożonych w usłudze Azure App Service:</span><span class="sxs-lookup"><span data-stu-id="b0e6d-134">Include the following NuGet packages to provide automatic logging features for apps deployed to Azure App Service:</span></span>

* <span data-ttu-id="b0e6d-135">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) używa [IHostingStartup,](xref:fundamentals/configuration/platform-specific-configuration) aby zapewnić ASP.NET core integracji light-up z usługą Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-135">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span></span> <span data-ttu-id="b0e6d-136">Dodane funkcje rejestrowania są `Microsoft.AspNetCore.AzureAppServicesIntegration` dostarczane przez pakiet.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-136">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span></span>
* <span data-ttu-id="b0e6d-137">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) wykonuje [AddAzureWebAppDiagnostics,](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) aby dodać dostawców rejestrowania `Microsoft.Extensions.Logging.AzureAppServices` diagnostyki usługi Azure App Service w pakiecie.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-137">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span></span>
* <span data-ttu-id="b0e6d-138">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) udostępnia implementacje rejestratorów do obsługi dzienników diagnostycznych usługi Azure App Service i funkcji przesyłania strumieniowego dziennika.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-138">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span></span>

<span data-ttu-id="b0e6d-139">Powyższe pakiety nie są dostępne w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b0e6d-139">The preceding packages aren't available from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="b0e6d-140">Aplikacje, które są przeznaczone `Microsoft.AspNetCore.App` dla platformy .NET Framework lub odwołują się do metapakiety, muszą jawnie odwoływać się do poszczególnych pakietów w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-140">Apps that target .NET Framework or reference the `Microsoft.AspNetCore.App` metapackage must explicitly reference the individual packages in the app's project file.</span></span>

## <a name="override-app-configuration-using-the-azure-portal"></a><span data-ttu-id="b0e6d-141">Zastępowanie konfiguracji aplikacji przy użyciu witryny Azure Portal</span><span class="sxs-lookup"><span data-stu-id="b0e6d-141">Override app configuration using the Azure Portal</span></span>

<span data-ttu-id="b0e6d-142">Ustawienia aplikacji w witrynie Azure Portal umożliwiają ustawianie zmiennych środowiskowych dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-142">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="b0e6d-143">Zmienne środowiskowe mogą być używane przez [dostawcę konfiguracji zmiennych środowiskowych](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b0e6d-143">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="b0e6d-144">Po utworzeniu lub zmodyfikowaniu ustawienia aplikacji w witrynie Azure Portal i wybraniu przycisku **Zapisz** aplikacja Azure zostanie ponownie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-144">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="b0e6d-145">Zmienna środowiskowa jest dostępna dla aplikacji po ponownym uruchomieniu usługi.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-145">The environment variable is available to the app after the service restarts.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b0e6d-146">Gdy aplikacja używa [ogólnego hosta,](xref:fundamentals/host/generic-host)zmienne środowiskowe są ładowane do konfiguracji aplikacji, gdy <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> jest wywoływana do tworzenia hosta.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-146">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables are loaded into the app's configuration when <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="b0e6d-147">Aby uzyskać więcej <xref:fundamentals/host/generic-host> informacji, zobacz i [dostawca konfiguracji zmiennych środowiskowych](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b0e6d-147">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b0e6d-148">Gdy aplikacja korzysta z [hosta sieci Web,](xref:fundamentals/host/web-host)zmienne środowiskowe <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> są ładowane do konfiguracji aplikacji, gdy jest wywoływana do tworzenia hosta.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-148">When an app uses the [Web Host](xref:fundamentals/host/web-host), environment variables are loaded into the app's configuration when <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="b0e6d-149">Aby uzyskać więcej <xref:fundamentals/host/web-host> informacji, zobacz i [dostawca konfiguracji zmiennych środowiskowych](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b0e6d-149">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="b0e6d-150">Scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="b0e6d-150">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="b0e6d-151">[Oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), które konfiguruje oprogramowanie pośredniczące nagłówków przesyłanych dalej podczas hostowania [poza procesem,](xref:host-and-deploy/iis/index#out-of-process-hosting-model)oraz ASP.NET Core Module są skonfigurowane do przesyłania dalej schematu (HTTP/HTTPS) i zdalnego adresu IP, z którego pochodzi żądanie.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-151">The [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), which configures Forwarded Headers Middleware when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="b0e6d-152">Dodatkowa konfiguracja może być wymagana dla aplikacji hostowanych za dodatkowymi serwerami proxy i modułami równoważenia obciążenia.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-152">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="b0e6d-153">Aby uzyskać więcej informacji, zobacz [Konfigurowanie ASP.NET Core do pracy z serwerami proxy i modułami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="b0e6d-153">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="b0e6d-154">Monitorowanie i rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="b0e6d-154">Monitoring and logging</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b0e6d-155">ASP.NET aplikacje Core wdrożone w usłudze App Service automatycznie odbierają rozszerzenie usługi App **Service, ASP.NET integrację rejestrowania rdzeni .**</span><span class="sxs-lookup"><span data-stu-id="b0e6d-155">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Integration**.</span></span> <span data-ttu-id="b0e6d-156">Rozszerzenie umożliwia rejestrowanie integracji dla ASP.NET aplikacji Core w usłudze Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-156">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b0e6d-157">ASP.NET aplikacje Core wdrożone w usłudze App Service automatycznie otrzymują rozszerzenie usługi App **Service, ASP.NET rozszerzenia rejestrowania rdzenia**.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-157">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Extensions**.</span></span> <span data-ttu-id="b0e6d-158">Rozszerzenie umożliwia rejestrowanie integracji dla ASP.NET aplikacji Core w usłudze Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-158">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

<span data-ttu-id="b0e6d-159">Aby uzyskać informacje dotyczące monitorowania, rejestrowania i rozwiązywania problemów, zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="b0e6d-159">For monitoring, logging, and troubleshooting information, see the following articles:</span></span>

[<span data-ttu-id="b0e6d-160">Monitorowanie aplikacji w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b0e6d-160">Monitor apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)  
<span data-ttu-id="b0e6d-161">Dowiedz się, jak przeglądać przydziały i metryki dla aplikacji i planów usługi App Service.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-161">Learn how to review quotas and metrics for apps and App Service plans.</span></span>

[<span data-ttu-id="b0e6d-162">Włączanie rejestrowania diagnostyki dla aplikacji w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b0e6d-162">Enable diagnostics logging for apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)  
<span data-ttu-id="b0e6d-163">Dowiedz się, jak włączyć i uzyskać dostęp do rejestrowania diagnostycznego dla kodów stanu HTTP, żądań nie powiodło się i aktywności serwera sieci Web.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-163">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="b0e6d-164">Zapoznaj się z typowymi metodami obsługi błędów w aplikacjach ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-164">Understand common approaches to handling errors in ASP.NET Core apps.</span></span>

<xref:test/troubleshoot-azure-iis>  
<span data-ttu-id="b0e6d-165">Dowiedz się, jak diagnozować problemy z wdrożeniami usługi Azure App Service za pomocą aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-165">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-iis-errors-reference>  
<span data-ttu-id="b0e6d-166">Zapoznaj się z typowymi błędami konfiguracji wdrażania aplikacji obsługiwanych przez usługę Azure App Service/usługi IIS, aby uzyskać porady dotyczące rozwiązywania problemów.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-166">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span></span>

## <a name="data-protection-key-ring-and-deployment-slots"></a><span data-ttu-id="b0e6d-167">Pierścień kluczy ochrony danych i miejsca wdrożenia</span><span class="sxs-lookup"><span data-stu-id="b0e6d-167">Data Protection key ring and deployment slots</span></span>

<span data-ttu-id="b0e6d-168">[Klucze ochrony danych](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) są zachowywane w folderze *%HOME%\ASP.NET\DataProtection-Keys.*</span><span class="sxs-lookup"><span data-stu-id="b0e6d-168">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="b0e6d-169">Ten folder jest wspierany przez magazyn sieciowy i jest synchronizowany na wszystkich komputerach hostujących aplikację.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-169">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span> <span data-ttu-id="b0e6d-170">Klucze nie są chronione w spoczynku.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-170">Keys aren't protected at rest.</span></span> <span data-ttu-id="b0e6d-171">Ten folder dostarcza pierścień klucza do wszystkich wystąpień aplikacji w jednym miejscu wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-171">This folder supplies the key ring to all instances of an app in a single deployment slot.</span></span> <span data-ttu-id="b0e6d-172">Oddzielne gniazda wdrażania, takie jak Staging i Production, nie współużytkuje pierścień klucza.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-172">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span>

<span data-ttu-id="b0e6d-173">Podczas wymiany między gniazdami wdrażania każdy system korzystający z ochrony danych nie będzie mógł odszyfrować przechowywanych danych przy użyciu pierścienia kluczy wewnątrz poprzedniego gniazda.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-173">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="b0e6d-174">ASP.NET Cookie Middleware wykorzystuje ochronę danych w celu ochrony swoich plików cookie.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-174">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span></span> <span data-ttu-id="b0e6d-175">Prowadzi to do wylogowania użytkowników z aplikacji korzystającej ze standardowego oprogramowania pośredniczącego ASP.NET plików cookie.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-175">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span></span> <span data-ttu-id="b0e6d-176">W przypadku rozwiązania z pierścieniem kluczy niezależnym od gniazda należy użyć zewnętrznego dostawcy pierścieni kluczy, takiego jak:</span><span class="sxs-lookup"><span data-stu-id="b0e6d-176">For a slot-independent key ring solution, use an external key ring provider, such as:</span></span>

* <span data-ttu-id="b0e6d-177">Azure Blob Storage</span><span class="sxs-lookup"><span data-stu-id="b0e6d-177">Azure Blob Storage</span></span>
* <span data-ttu-id="b0e6d-178">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b0e6d-178">Azure Key Vault</span></span>
* <span data-ttu-id="b0e6d-179">Sklep SQL</span><span class="sxs-lookup"><span data-stu-id="b0e6d-179">SQL store</span></span>
* <span data-ttu-id="b0e6d-180">Pamięć podręczna Redis</span><span class="sxs-lookup"><span data-stu-id="b0e6d-180">Redis cache</span></span>

<span data-ttu-id="b0e6d-181">Aby uzyskać więcej informacji, zobacz <xref:security/data-protection/implementation/key-storage-providers>.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-181">For more information, see <xref:security/data-protection/implementation/key-storage-providers>.</span></span>
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-an-aspnet-core-app-that-uses-a-net-core-preview"></a><span data-ttu-id="b0e6d-182">Wdrażanie aplikacji ASP.NET Core korzystającej z wersji zapoznawczej .NET Core</span><span class="sxs-lookup"><span data-stu-id="b0e6d-182">Deploy an ASP.NET Core app that uses a .NET Core preview</span></span>

<span data-ttu-id="b0e6d-183">Aby wdrożyć aplikację, która używa wersji zapoznawczej programu .NET Core, zobacz następujące zasoby.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-183">To deploy an app that uses a preview release of .NET Core, see the following resources.</span></span> <span data-ttu-id="b0e6d-184">Te podejścia są również używane, gdy środowisko wykonawcze jest dostępne, ale zestaw SDK nie został zainstalowany w usłudze Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-184">These approaches are also used when the runtime is available but the SDK hasn't been installed on Azure App Service.</span></span>

* [<span data-ttu-id="b0e6d-185">Określ wersję SDK rdzenia platformy .NET przy użyciu potoków platformy Azure</span><span class="sxs-lookup"><span data-stu-id="b0e6d-185">Specify the .NET Core SDK Version using Azure Pipelines</span></span>](#specify-the-net-core-sdk-version-using-azure-pipelines)
* [<span data-ttu-id="b0e6d-186">Wdrażanie samodzielnej aplikacji w wersji zapoznawczej</span><span class="sxs-lookup"><span data-stu-id="b0e6d-186">Deploy a self-contained preview app</span></span>](#deploy-a-self-contained-preview-app)
* [<span data-ttu-id="b0e6d-187">Używanie platformy Docker z aplikacjami sieci Web dla kontenerów</span><span class="sxs-lookup"><span data-stu-id="b0e6d-187">Use Docker with Web Apps for containers</span></span>](#use-docker-with-web-apps-for-containers)
* [<span data-ttu-id="b0e6d-188">Instalowanie rozszerzenia witryny w wersji zapoznawczej</span><span class="sxs-lookup"><span data-stu-id="b0e6d-188">Install the preview site extension</span></span>](#install-the-preview-site-extension)

<span data-ttu-id="b0e6d-189">Zobacz [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) dla wersji ASP.NET Core dostępnej w usłudze Azure App.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-189">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a><span data-ttu-id="b0e6d-190">Określ wersję SDK rdzenia platformy .NET przy użyciu potoków platformy Azure</span><span class="sxs-lookup"><span data-stu-id="b0e6d-190">Specify the .NET Core SDK Version using Azure Pipelines</span></span>

<span data-ttu-id="b0e6d-191">Użyj [scenariuszy ciągłej integracji/dysku CD usługi Azure App Service,](/azure/app-service/deploy-continuous-deployment) aby skonfigurować kompilację ciągłej integracji z platformą Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-191">Use [Azure App Service CI/CD scenarios](/azure/app-service/deploy-continuous-deployment) to set up a continuous integration build with Azure DevOps.</span></span> <span data-ttu-id="b0e6d-192">Po utworzeniu kompilacji azure devops opcjonalnie skonfiguruj kompilację, aby używała określonej wersji SDK.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-192">After the Azure DevOps build is created, optionally configure the build to use a specific SDK version.</span></span> 

#### <a name="specify-the-net-core-sdk-version"></a><span data-ttu-id="b0e6d-193">Określ wersję SDK rdzenia .NET</span><span class="sxs-lookup"><span data-stu-id="b0e6d-193">Specify the .NET Core SDK version</span></span>

<span data-ttu-id="b0e6d-194">Podczas tworzenia kompilacji usługi Azure DevOps przy użyciu centrum wdrażania `Restore`usługi `Build` `Test`App `Publish`Service domyślny potok kompilacji zawiera kroki dla , , i .</span><span class="sxs-lookup"><span data-stu-id="b0e6d-194">When using the App Service deployment center to create an Azure DevOps build, the default build pipeline includes steps for `Restore`, `Build`, `Test`, and `Publish`.</span></span> <span data-ttu-id="b0e6d-195">Aby określić wersję SDK, wybierz przycisk **Dodaj (+)** na liście zadań agenta, aby dodać nowy krok.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-195">To specify the SDK version, select the **Add (+)** button in the Agent job list to add a new step.</span></span> <span data-ttu-id="b0e6d-196">Wyszukaj **.NET Core SDK** na pasku wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-196">Search for **.NET Core SDK** in the search bar.</span></span> 

![Dodawanie kroku net core sdk](index/add-sdk-step.png)

<span data-ttu-id="b0e6d-198">Przenieś krok do pierwszej pozycji w kompilacji, tak aby kroki po nim używać określonej wersji .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-198">Move the step into the first position in the build so that the steps following it use the specified version of the .NET Core SDK.</span></span> <span data-ttu-id="b0e6d-199">Określ wersję sdk .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-199">Specify the version of the .NET Core SDK.</span></span> <span data-ttu-id="b0e6d-200">W tym przykładzie zestaw SDK jest ustawiony na `3.0.100`.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-200">In this example, the SDK is set to `3.0.100`.</span></span>

![Ukończony krok SDK](index/sdk-step-first-place.png)

<span data-ttu-id="b0e6d-202">Aby opublikować [samodzielne wdrożenie (SCD),](/dotnet/core/deploying/#self-contained-deployments-scd)skonfiguruj scd w `Publish` kroku i podaj identyfikator środowiska wykonawczego [(RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="b0e6d-202">To publish a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), configure SCD in the `Publish` step and provide the [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

![Samodzielne publikowanie](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a><span data-ttu-id="b0e6d-204">Wdrażanie samodzielnej aplikacji w wersji zapoznawczej</span><span class="sxs-lookup"><span data-stu-id="b0e6d-204">Deploy a self-contained preview app</span></span>

<span data-ttu-id="b0e6d-205">[Samodzielne wdrożenie (SCD),](/dotnet/core/deploying/#self-contained-deployments-scd) które jest przeznaczone dla środowiska uruchomieniowego w wersji zapoznawczej, przenosi środowisko uruchomieniowe w wersji zapoznawczej we wdrożeniu.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-205">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span></span>

<span data-ttu-id="b0e6d-206">Podczas wdrażania aplikacji niezależnej:</span><span class="sxs-lookup"><span data-stu-id="b0e6d-206">When deploying a self-contained app:</span></span>

* <span data-ttu-id="b0e6d-207">Witryna w usłudze Azure App Service nie wymaga [rozszerzenia witryny w wersji zapoznawczej.](#install-the-preview-site-extension)</span><span class="sxs-lookup"><span data-stu-id="b0e6d-207">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="b0e6d-208">Aplikacja musi być opublikowana zgodnie z innym podejściem niż podczas publikowania dla [wdrożenia zależnego od struktury (FDD).](/dotnet/core/deploying#framework-dependent-deployments-fdd)</span><span class="sxs-lookup"><span data-stu-id="b0e6d-208">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="b0e6d-209">Postępuj zgodnie ze wskazówkami w [sekcji Wdrażanie aplikacji samodzielnej.](#deploy-the-app-self-contained)</span><span class="sxs-lookup"><span data-stu-id="b0e6d-209">Follow the guidance in the [Deploy the app self-contained](#deploy-the-app-self-contained) section.</span></span>

### <a name="use-docker-with-web-apps-for-containers"></a><span data-ttu-id="b0e6d-210">Używanie platformy Docker z aplikacjami sieci Web dla kontenerów</span><span class="sxs-lookup"><span data-stu-id="b0e6d-210">Use Docker with Web Apps for containers</span></span>

<span data-ttu-id="b0e6d-211">[Centrum platformy Docker](https://hub.docker.com/r/microsoft/aspnetcore/) zawiera najnowsze obrazy platformy Docker w wersji zapoznawczej.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-211">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span></span> <span data-ttu-id="b0e6d-212">Obrazy mogą być używane jako obraz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-212">The images can be used as a base image.</span></span> <span data-ttu-id="b0e6d-213">Użyj obrazu i wdrożyć w aplikacjach sieci Web dla kontenerów normalnie.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-213">Use the image and deploy to Web Apps for Containers normally.</span></span>

### <a name="install-the-preview-site-extension"></a><span data-ttu-id="b0e6d-214">Instalowanie rozszerzenia witryny w wersji zapoznawczej</span><span class="sxs-lookup"><span data-stu-id="b0e6d-214">Install the preview site extension</span></span>

<span data-ttu-id="b0e6d-215">Jeśli wystąpi problem przy użyciu rozszerzenia witryny w wersji zapoznawczej, otwórz [problem dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/issues).</span><span class="sxs-lookup"><span data-stu-id="b0e6d-215">If a problem occurs using the preview site extension, open an [dotnet/AspNetCore issue](https://github.com/dotnet/AspNetCore/issues).</span></span>

1. <span data-ttu-id="b0e6d-216">W witrynie Azure Portal przejdź do usługi App Service.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-216">From the Azure Portal, navigate to the App Service.</span></span>
1. <span data-ttu-id="b0e6d-217">Wybierz aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-217">Select the web app.</span></span>
1. <span data-ttu-id="b0e6d-218">Wpisz "ex" w polu wyszukiwania, aby filtrować "Rozszerzenia" lub przewiń w dół listę narzędzi do zarządzania.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-218">Type "ex" in the search box to filter for "Extensions" or scroll down the list of management tools.</span></span>
1. <span data-ttu-id="b0e6d-219">Wybierz pozycję **Rozszerzenia**.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-219">Select **Extensions**.</span></span>
1. <span data-ttu-id="b0e6d-220">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-220">Select **Add**.</span></span>
1. <span data-ttu-id="b0e6d-221">Wybierz z listy rozszerzenie **ASP.NET Core {X.Y} ({x64|x86}),** gdzie `{X.Y}` znajduje się wersja ASP.NET `{x64|x86}` Core preview i określa platformę.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-221">Select the **ASP.NET Core {X.Y} ({x64|x86}) Runtime** extension from the list, where `{X.Y}` is the ASP.NET Core preview version and `{x64|x86}` specifies the platform.</span></span>
1. <span data-ttu-id="b0e6d-222">Wybierz **przycisk OK,** aby zaakceptować warunki prawne.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-222">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="b0e6d-223">Wybierz **przycisk OK,** aby zainstalować rozszerzenie.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-223">Select **OK** to install the extension.</span></span>

<span data-ttu-id="b0e6d-224">Po zakończeniu operacji zostanie zainstalowany najnowszy podgląd rdzenia .NET.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-224">When the operation completes, the latest .NET Core preview is installed.</span></span> <span data-ttu-id="b0e6d-225">Sprawdź instalację:</span><span class="sxs-lookup"><span data-stu-id="b0e6d-225">Verify the installation:</span></span>

1. <span data-ttu-id="b0e6d-226">Wybierz opcję **Narzędzia zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-226">Select **Advanced Tools**.</span></span>
1. <span data-ttu-id="b0e6d-227">Wybierz **pozycję Przejdź** w obszarze Narzędzia **zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-227">Select **Go** in **Advanced Tools**.</span></span>
1. <span data-ttu-id="b0e6d-228">Wybierz element menu >  **konsoli debugowania**programu**PowerShell.**</span><span class="sxs-lookup"><span data-stu-id="b0e6d-228">Select the **Debug console** > **PowerShell** menu item.</span></span>
1. <span data-ttu-id="b0e6d-229">W wierszu programu PowerShell wykonaj następujące polecenie.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-229">At the PowerShell prompt, execute the following command.</span></span> <span data-ttu-id="b0e6d-230">Zastąp ASP.NET core wersji `{X.Y}` środowiska uruchomieniowego i platformy w `{PLATFORM}` poleceniu:</span><span class="sxs-lookup"><span data-stu-id="b0e6d-230">Substitute the ASP.NET Core runtime version for `{X.Y}` and the platform for `{PLATFORM}` in the command:</span></span>

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   <span data-ttu-id="b0e6d-231">Polecenie zwraca `True` po zainstalowaniu środowiska wykonawczego podglądu x64.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-231">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="b0e6d-232">Architektura platformy (x86/x64) aplikacji usługi App Services jest ustawiona w ustawieniach aplikacji w witrynie Azure Portal dla aplikacji, które są hostowane w warstwie obliczeniowej serii A (Basic) lub wyższej.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-232">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="b0e6d-233">Upewnij się, że ustawienia publikowania aplikacji (na przykład w profilu publikowania w programie Visual Studio [(pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) są zgodne z ustawieniem w konfiguracji usługi aplikacji w witrynie Azure portal.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-233">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure portal.</span></span>
>
> <span data-ttu-id="b0e6d-234">Jeśli aplikacja jest uruchamiana w trybie w toku, a architektura platformy jest skonfigurowana dla 64-bitowych (x64), ASP.NET Core Module używa 64-bitowego środowiska wykonawczego podglądu, jeśli jest obecny.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-234">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span></span> <span data-ttu-id="b0e6d-235">Zainstaluj rozszerzenie **środowiska wykonawczego ASP.NET Core {X.Y} (x64)** przy użyciu witryny Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-235">Install the **ASP.NET Core {X.Y} (x64) Runtime** extension using the Azure Portal.</span></span>
>
> <span data-ttu-id="b0e6d-236">Po zainstalowaniu środowiska wykonawczego w wersji zapoznawczej x64 uruchom następujące polecenie w oknie polecenia programu Azure Kudu PowerShell, aby zweryfikować instalację.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-236">After installing the x64 preview runtime, run the following command in the Azure Kudu PowerShell command window to verify the installation.</span></span> <span data-ttu-id="b0e6d-237">Zastąp ASP.NET core wersji `{X.Y}` środowiska uruchomieniowego w następującym poleceniu:</span><span class="sxs-lookup"><span data-stu-id="b0e6d-237">Substitute the ASP.NET Core runtime version for `{X.Y}` in the following command:</span></span>
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> <span data-ttu-id="b0e6d-238">Polecenie zwraca `True` po zainstalowaniu środowiska wykonawczego podglądu x64.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-238">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="b0e6d-239">**ASP.NET rozszerzenia core** umożliwia dodatkowe funkcje dla ASP.NET Core w usłudze Azure App Services, takie jak włączanie rejestrowania platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-239">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span></span> <span data-ttu-id="b0e6d-240">Rozszerzenie jest instalowane automatycznie podczas wdrażania z programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-240">The extension is installed automatically when deploying from Visual Studio.</span></span> <span data-ttu-id="b0e6d-241">Jeśli rozszerzenie nie jest zainstalowane, zainstaluj je dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-241">If the extension isn't installed, install it for the app.</span></span>

<span data-ttu-id="b0e6d-242">**Używanie rozszerzenia witryny podglądu z szablonem ARM**</span><span class="sxs-lookup"><span data-stu-id="b0e6d-242">**Use the preview site extension with an ARM template**</span></span>

<span data-ttu-id="b0e6d-243">Jeśli szablon ARM jest używany do tworzenia `siteextensions` i wdrażania aplikacji, typ zasobu może służyć do dodawania rozszerzenia witryny do aplikacji sieci web.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-243">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span></span> <span data-ttu-id="b0e6d-244">Przykład:</span><span class="sxs-lookup"><span data-stu-id="b0e6d-244">For example:</span></span>

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="b0e6d-245">Publikowanie i wdrażanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="b0e6d-245">Publish and deploy the app</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b0e6d-246">W przypadku wdrożenia 64-bitowego:</span><span class="sxs-lookup"><span data-stu-id="b0e6d-246">For a 64-bit deployment:</span></span>

* <span data-ttu-id="b0e6d-247">Użyj 64-bitowego sdk .NET Core do tworzenia aplikacji 64-bitowej.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-247">Use a 64-bit .NET Core SDK to build a 64-bit app.</span></span>
* <span data-ttu-id="b0e6d-248">Ustaw **platformę** na **64 bit** w**ustawieniach ogólnych** **konfiguracji** > usługi app service .</span><span class="sxs-lookup"><span data-stu-id="b0e6d-248">Set the **Platform** to **64 Bit** in the App Service's **Configuration** > **General settings**.</span></span> <span data-ttu-id="b0e6d-249">Aplikacja musi używać planu usługi Basic lub wyższej, aby umożliwić wybór bitowości platformy.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-249">The app must use a Basic or higher service plan to enable the choice of platform bitness.</span></span>

::: moniker-end

### <a name="deploy-the-app-framework-dependent"></a><span data-ttu-id="b0e6d-250">Wdrażanie aplikacji zależne od struktury aplikacji</span><span class="sxs-lookup"><span data-stu-id="b0e6d-250">Deploy the app framework-dependent</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b0e6d-251">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0e6d-251">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b0e6d-252">Wybierz **polecenie Buduj** > **publikuj {Nazwa aplikacji}** na pasku narzędzi programu Visual Studio lub kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz pozycję **Publikuj**.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-252">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="b0e6d-253">W oknie **dialogowym Wybierz miejsce docelowe publikowania** upewnij się, że usługa **App Service** jest zaznaczona.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-253">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="b0e6d-254">Wybierz **pozycję Zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-254">Select **Advanced**.</span></span> <span data-ttu-id="b0e6d-255">Zostanie otwarte okno dialogowe **Publikowanie.**</span><span class="sxs-lookup"><span data-stu-id="b0e6d-255">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="b0e6d-256">W oknie dialogowym **Publikowanie:**</span><span class="sxs-lookup"><span data-stu-id="b0e6d-256">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="b0e6d-257">Upewnij się, że wybrano **konfigurację wydania.**</span><span class="sxs-lookup"><span data-stu-id="b0e6d-257">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="b0e6d-258">Otwórz listę rozwijaną **Tryb wdrażania** i wybierz opcję **Zależne od struktury**.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-258">Open the **Deployment Mode** drop-down list and select **Framework-Dependent**.</span></span>
   * <span data-ttu-id="b0e6d-259">Wybierz **pozycję Przenośny** jako **docelowy czas pracy**.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-259">Select **Portable** as the **Target Runtime**.</span></span>
   * <span data-ttu-id="b0e6d-260">Jeśli chcesz usunąć dodatkowe pliki po wdrożeniu, otwórz **opcje publikowania plików** i zaznacz pole wyboru, aby usunąć dodatkowe pliki w miejscu docelowym.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-260">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="b0e6d-261">Wybierz **pozycję Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-261">Select **Save**.</span></span>
1. <span data-ttu-id="b0e6d-262">Utwórz nową witrynę lub zaktualizuj istniejącą witrynę, wykonując pozostałe monity kreatora publikowania.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-262">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b0e6d-263">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="b0e6d-263">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="b0e6d-264">W pliku projektu nie określaj [identyfikatora środowiska uruchomieniowego (RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="b0e6d-264">In the project file, don't specify a [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

1. <span data-ttu-id="b0e6d-265">W powłoce poleceń opublikuj aplikację w konfiguracji wydania za pomocą polecenia [publikowania dotnet.](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="b0e6d-265">From a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="b0e6d-266">W poniższym przykładzie aplikacja jest publikowana jako aplikacja zależna od struktury:</span><span class="sxs-lookup"><span data-stu-id="b0e6d-266">In the following example, the app is published as a framework-dependent app:</span></span>

   ```console
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="b0e6d-267">Przenieś zawartość *katalogu bin/release/{TARGET FRAMEWORK}/publish* do witryny w usłudze App Service.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-267">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="b0e6d-268">Jeśli przeciągniesz zawartość folderu *publikowania* z lokalnego dysku twardego lub udziału sieciowego bezpośrednio `D:\home\site\wwwroot` do usługi App Service w konsoli [Kudu,](https://github.com/projectkudu/kudu/wiki) przeciągnij pliki do folderu w konsoli Kudu.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-268">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the [Kudu](https://github.com/projectkudu/kudu/wiki) console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

### <a name="deploy-the-app-self-contained"></a><span data-ttu-id="b0e6d-269">Wdrażanie aplikacji niezależnej</span><span class="sxs-lookup"><span data-stu-id="b0e6d-269">Deploy the app self-contained</span></span>

<span data-ttu-id="b0e6d-270">Użyj programu Visual Studio lub interfejsu wiersza polecenia .NET Core dla [samodzielnego wdrożenia (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="b0e6d-270">Use Visual Studio or the .NET Core CLI for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b0e6d-271">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0e6d-271">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b0e6d-272">Wybierz **polecenie Buduj** > **publikuj {Nazwa aplikacji}** na pasku narzędzi programu Visual Studio lub kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz pozycję **Publikuj**.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-272">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="b0e6d-273">W oknie **dialogowym Wybierz miejsce docelowe publikowania** upewnij się, że usługa **App Service** jest zaznaczona.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-273">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="b0e6d-274">Wybierz **pozycję Zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-274">Select **Advanced**.</span></span> <span data-ttu-id="b0e6d-275">Zostanie otwarte okno dialogowe **Publikowanie.**</span><span class="sxs-lookup"><span data-stu-id="b0e6d-275">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="b0e6d-276">W oknie dialogowym **Publikowanie:**</span><span class="sxs-lookup"><span data-stu-id="b0e6d-276">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="b0e6d-277">Upewnij się, że wybrano **konfigurację wydania.**</span><span class="sxs-lookup"><span data-stu-id="b0e6d-277">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="b0e6d-278">Otwórz listę rozwijaną **Tryb wdrażania** i wybierz opcję **Samodzielny**.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-278">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span></span>
   * <span data-ttu-id="b0e6d-279">Wybierz docelowy czas wykonywania z listy rozwijanej **Docelowy czas wykonywania.**</span><span class="sxs-lookup"><span data-stu-id="b0e6d-279">Select the target runtime from the **Target Runtime** drop-down list.</span></span> <span data-ttu-id="b0e6d-280">Wartość domyślna to `win-x86`.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-280">The default is `win-x86`.</span></span>
   * <span data-ttu-id="b0e6d-281">Jeśli chcesz usunąć dodatkowe pliki po wdrożeniu, otwórz **opcje publikowania plików** i zaznacz pole wyboru, aby usunąć dodatkowe pliki w miejscu docelowym.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-281">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="b0e6d-282">Wybierz **pozycję Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-282">Select **Save**.</span></span>
1. <span data-ttu-id="b0e6d-283">Utwórz nową witrynę lub zaktualizuj istniejącą witrynę, wykonując pozostałe monity kreatora publikowania.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-283">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b0e6d-284">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="b0e6d-284">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="b0e6d-285">W pliku projektu określ jeden lub więcej [identyfikatorów środowiska uruchomieniowego (RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="b0e6d-285">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="b0e6d-286">Użyj `<RuntimeIdentifier>` (liczby pojedynczej) dla `<RuntimeIdentifiers>` pojedynczego rid lub użyj (liczba mnoga), aby zapewnić listę identyfikatorów RID rozdzielanych średnikami.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-286">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span></span> <span data-ttu-id="b0e6d-287">W poniższym przykładzie `win-x86` określono identyfikator RID:</span><span class="sxs-lookup"><span data-stu-id="b0e6d-287">In the following example, the `win-x86` RID is specified:</span></span>

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. <span data-ttu-id="b0e6d-288">W powłoce poleceń opublikuj aplikację w konfiguracji wydania dla środowiska wykonawczego hosta za pomocą polecenia [publikowania dotnet.](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="b0e6d-288">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="b0e6d-289">W poniższym przykładzie aplikacja jest `win-x86` publikowana dla rid.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-289">In the following example, the app is published for the `win-x86` RID.</span></span> <span data-ttu-id="b0e6d-290">Identyfikator RID podany `--runtime` do opcji musi `<RuntimeIdentifier>` być `<RuntimeIdentifiers>`podany we właściwości (lub) w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-290">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span></span>

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. <span data-ttu-id="b0e6d-291">Przenieś zawartość *katalogu bin/release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory do witryny w usłudze App Service.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-291">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="b0e6d-292">Jeśli przeciągniesz zawartość folderu *publikowania* z lokalnego dysku twardego lub udziału sieciowego bezpośrednio `D:\home\site\wwwroot` do usługi App Service w konsoli Kudu, przeciągnij pliki do folderu w konsoli Kudu.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-292">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the Kudu console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

## <a name="protocol-settings-https"></a><span data-ttu-id="b0e6d-293">Ustawienia protokołu (HTTPS)</span><span class="sxs-lookup"><span data-stu-id="b0e6d-293">Protocol settings (HTTPS)</span></span>

<span data-ttu-id="b0e6d-294">Powiązania bezpiecznego protokołu umożliwiają określenie certyfikatu, który ma być używany podczas odpowiadania na żądania za pośrednictwem protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-294">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span></span> <span data-ttu-id="b0e6d-295">Powiązanie wymaga prawidłowego certyfikatu prywatnego (*.pfx*) wystawionego dla określonej nazwy hosta.</span><span class="sxs-lookup"><span data-stu-id="b0e6d-295">Binding requires a valid private certificate (*.pfx*) issued for the specific hostname.</span></span> <span data-ttu-id="b0e6d-296">Aby uzyskać więcej informacji, zobacz [Samouczek: Powiąż istniejący niestandardowy certyfikat SSL z usługą Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="b0e6d-296">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="b0e6d-297">Przekształcanie pliku web.config</span><span class="sxs-lookup"><span data-stu-id="b0e6d-297">Transform web.config</span></span>

<span data-ttu-id="b0e6d-298">Jeśli chcesz przekształcić *web.config* przy publikowaniu (na przykład ustawić zmienne środowiskowe na <xref:host-and-deploy/iis/transform-webconfig>podstawie konfiguracji, profilu lub środowiska), zobacz .</span><span class="sxs-lookup"><span data-stu-id="b0e6d-298">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b0e6d-299">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="b0e6d-299">Additional resources</span></span>

* [<span data-ttu-id="b0e6d-300">Omówienie usługi App Service</span><span class="sxs-lookup"><span data-stu-id="b0e6d-300">App Service overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="b0e6d-301">Usługa Azure App Service: najlepsze miejsce do hostowania aplikacji .NET (55-minutowy klip wideo z omówieniem)</span><span class="sxs-lookup"><span data-stu-id="b0e6d-301">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span></span>](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [<span data-ttu-id="b0e6d-302">Usługa Azure Friday: Środowisko diagnostyki i rozwiązywania problemów usługi Azure App Service (12-minutowy film wideo)</span><span class="sxs-lookup"><span data-stu-id="b0e6d-302">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [<span data-ttu-id="b0e6d-303">Omówienie diagnostyki usługi Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b0e6d-303">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

<span data-ttu-id="b0e6d-304">Usługa Azure App Service w systemie Windows Server korzysta z [internetowych usług informacyjnych (IIS).](https://www.iis.net/)</span><span class="sxs-lookup"><span data-stu-id="b0e6d-304">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span></span> <span data-ttu-id="b0e6d-305">Następujące tematy odnoszą się do podstawowej technologii IIS:</span><span class="sxs-lookup"><span data-stu-id="b0e6d-305">The following topics pertain to the underlying IIS technology:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [<span data-ttu-id="b0e6d-306">Windows Server — zawartość administratora IT dla bieżących i poprzednich wersji</span><span class="sxs-lookup"><span data-stu-id="b0e6d-306">Windows Server - IT administrator content for current and previous releases</span></span>](/windows-server/windows-server-versions)
