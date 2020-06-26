---
title: Wdróż aplikacje ASP.NET Core w Azure App Service
author: bradygaster
description: Ten artykuł zawiera linki do hosta platformy Azure i wdrażania zasobów.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: 9ffeebbf8125ddac5d6e621e411c4e86c5bd34b1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399312"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a><span data-ttu-id="c1e0f-103">Wdróż aplikacje ASP.NET Core w Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c1e0f-103">Deploy ASP.NET Core apps to Azure App Service</span></span>

<span data-ttu-id="c1e0f-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) to [usługa platformy obliczeniowej w chmurze firmy Microsoft](https://azure.microsoft.com/) do hostowania aplikacji sieci web, w tym ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span>

## <a name="useful-resources"></a><span data-ttu-id="c1e0f-105">Przydatne zasoby</span><span class="sxs-lookup"><span data-stu-id="c1e0f-105">Useful resources</span></span>

<span data-ttu-id="c1e0f-106">[Dokumentacja App Service](/azure/app-service/) jest domem dla dokumentacji usługi Azure Apps, samouczków, przykładów, przewodników i innych zasobów.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-106">[App Service Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span></span> <span data-ttu-id="c1e0f-107">Dwa istotne samouczki odnoszące się do hostingu aplikacji ASP.NET Core są następujące:</span><span class="sxs-lookup"><span data-stu-id="c1e0f-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span></span>

[<span data-ttu-id="c1e0f-108">Tworzenie aplikacji internetowej ASP.NET Core na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="c1e0f-108">Create an ASP.NET Core web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)  
<span data-ttu-id="c1e0f-109">Użyj programu Visual Studio, aby utworzyć i wdrożyć aplikację sieci Web ASP.NET Core do Azure App Service w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span></span>

[<span data-ttu-id="c1e0f-110">Tworzenie aplikacji platformy ASP.NET Core w usłudze App Service dla systemu Linux</span><span class="sxs-lookup"><span data-stu-id="c1e0f-110">Create an ASP.NET Core app in App Service on Linux</span></span>](/azure/app-service/containers/quickstart-dotnetcore)  
<span data-ttu-id="c1e0f-111">Użyj wiersza polecenia, aby utworzyć i wdrożyć aplikację sieci Web ASP.NET Core do Azure App Service w systemie Linux.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span></span>

<span data-ttu-id="c1e0f-112">Zapoznaj się z wersją ASP.NET Core dostępną w usłudze Azure App Service, [ASP.NET Core na pulpicie nawigacyjnym app Service](https://aspnetcoreon.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="c1e0f-112">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

<span data-ttu-id="c1e0f-113">Zasubskrybuj repozytorium [App Service Anonsy](https://github.com/Azure/app-service-announcements/) i monitoruj problemy.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-113">Subscribe to the [App Service Announcements](https://github.com/Azure/app-service-announcements/) repository and monitor the issues.</span></span> <span data-ttu-id="c1e0f-114">Zespół App Service regularnie ogłasza anonse i scenariusze docierające do App Service.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-114">The App Service team regularly posts announcements and scenarios arriving in App Service.</span></span>

<span data-ttu-id="c1e0f-115">Następujące artykuły są dostępne w dokumentacji ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="c1e0f-115">The following articles are available in ASP.NET Core documentation:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="c1e0f-116">Dowiedz się, jak opublikować aplikację ASP.NET Core, aby Azure App Service przy użyciu programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-116">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
<span data-ttu-id="c1e0f-117">Dowiedz się, jak utworzyć aplikację internetową ASP.NET Core przy użyciu programu Visual Studio i wdrożyć ją do Azure App Service przy użyciu narzędzia Git do ciągłego wdrażania.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-117">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span></span>

[<span data-ttu-id="c1e0f-118">Tworzenie pierwszego potoku</span><span class="sxs-lookup"><span data-stu-id="c1e0f-118">Create your first pipeline</span></span>](/azure/devops/pipelines/get-started-yaml)  
<span data-ttu-id="c1e0f-119">Skonfiguruj kompilację CI dla aplikacji ASP.NET Core, a następnie Utwórz wersję ciągłego wdrażania do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-119">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span></span>

[<span data-ttu-id="c1e0f-120">Piaskownica aplikacji sieci Web platformy Azure</span><span class="sxs-lookup"><span data-stu-id="c1e0f-120">Azure Web App sandbox</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
<span data-ttu-id="c1e0f-121">Odkryj ograniczenia wykonywania w środowisku uruchomieniowym Azure App Service wymuszane przez platformę Azure Apps.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-121">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span></span>

<xref:test/troubleshoot>  
<span data-ttu-id="c1e0f-122">Zrozumienie i rozwiązywanie problemów z ostrzeżeniami i błędami w projektach ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-122">Understand and troubleshoot warnings and errors with ASP.NET Core projects.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="c1e0f-123">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="c1e0f-123">Application configuration</span></span>

### <a name="platform"></a><span data-ttu-id="c1e0f-124">Platforma</span><span class="sxs-lookup"><span data-stu-id="c1e0f-124">Platform</span></span>

<span data-ttu-id="c1e0f-125">Architektura platformy (x86/x64) aplikacji App Services jest ustawiana w ustawieniach aplikacji w witrynie Azure Portal dla aplikacji hostowanych w warstwie obliczeniowej serii A (podstawowa) lub wyższej.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-125">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="c1e0f-126">Upewnij się, że ustawienia publikowania aplikacji (na przykład w profilu publikacji programu Visual Studio [(. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) są zgodne z ustawieniami w konfiguracji usługi aplikacji w witrynie Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-126">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure Portal.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c1e0f-127">W Azure App Service są obecne środowiska uruchomieniowe dla aplikacji 64-bitowych (x64) i 32-bitowych (x86).</span><span class="sxs-lookup"><span data-stu-id="c1e0f-127">Runtimes for 64-bit (x64) and 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="c1e0f-128">[Zestaw .NET Core SDK](/dotnet/core/sdk) dostępne w App Service to 32-bitowe, ale można wdrożyć aplikacje 64-bitowe utworzone lokalnie za pomocą konsoli [kudu](https://github.com/projectkudu/kudu/wiki) lub procesu publikowania w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-128">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit, but you can deploy 64-bit apps built locally using the [Kudu](https://github.com/projectkudu/kudu/wiki) console or the publish process in Visual Studio.</span></span> <span data-ttu-id="c1e0f-129">Aby uzyskać więcej informacji, zobacz sekcję [Publikowanie i wdrażanie aplikacji](#publish-and-deploy-the-app) .</span><span class="sxs-lookup"><span data-stu-id="c1e0f-129">For more information, see the [Publish and deploy the app](#publish-and-deploy-the-app) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c1e0f-130">W przypadku aplikacji z natywnymi zależnościami w Azure App Service są obecne środowiska uruchomieniowe dla 32-bitowych (x86) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-130">For apps with native dependencies, runtimes for 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="c1e0f-131">[Zestaw .NET Core SDK](/dotnet/core/sdk) dostępne w App Service to 32-bitowe.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-131">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit.</span></span>

::: moniker-end

<span data-ttu-id="c1e0f-132">Aby uzyskać więcej informacji na temat składników .NET Core i metod dystrybucji, takich jak informacje na temat środowiska uruchomieniowego .NET Core i zestaw .NET Core SDK, zobacz [Informacje o kompozycji platformy .NET Core:](/dotnet/core/about#composition).</span><span class="sxs-lookup"><span data-stu-id="c1e0f-132">For more information on .NET Core framework components and distribution methods, such as information on the .NET Core runtime and the .NET Core SDK, see [About .NET Core: Composition](/dotnet/core/about#composition).</span></span>

### <a name="packages"></a><span data-ttu-id="c1e0f-133">Pakiety</span><span class="sxs-lookup"><span data-stu-id="c1e0f-133">Packages</span></span>

<span data-ttu-id="c1e0f-134">Dołącz następujące pakiety NuGet, aby udostępnić funkcje automatycznego rejestrowania dla aplikacji wdrożonych w Azure App Service:</span><span class="sxs-lookup"><span data-stu-id="c1e0f-134">Include the following NuGet packages to provide automatic logging features for apps deployed to Azure App Service:</span></span>

* <span data-ttu-id="c1e0f-135">[Microsoft. AspNetCore. AzureAppServices. HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) używa [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) , aby zapewnić ASP.NET Coreą integrację z Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-135">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span></span> <span data-ttu-id="c1e0f-136">Dodane funkcje rejestrowania są udostępniane przez `Microsoft.AspNetCore.AzureAppServicesIntegration` pakiet.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-136">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span></span>
* <span data-ttu-id="c1e0f-137">[Microsoft. AspNetCore. AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) wykonuje [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) , aby dodać Azure App Service dostawców rejestrowania diagnostyki w `Microsoft.Extensions.Logging.AzureAppServices` pakiecie.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-137">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span></span>
* <span data-ttu-id="c1e0f-138">[Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) udostępnia implementacje rejestratorów umożliwiające obsługę dzienników diagnostyki Azure App Service i funkcji przesyłania strumieniowego dzienników.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-138">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span></span>

<span data-ttu-id="c1e0f-139">Poprzednie pakiety nie są dostępne w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c1e0f-139">The preceding packages aren't available from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="c1e0f-140">Aplikacje, które są przeznaczone dla elementu .NET Framework lub odwołują się do `Microsoft.AspNetCore.App` pakietu, muszą jawnie odwoływać się do poszczególnych pakietów w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-140">Apps that target .NET Framework or reference the `Microsoft.AspNetCore.App` metapackage must explicitly reference the individual packages in the app's project file.</span></span>

## <a name="override-app-configuration-using-the-azure-portal"></a><span data-ttu-id="c1e0f-141">Przesłoń konfigurację aplikacji przy użyciu witryny Azure Portal</span><span class="sxs-lookup"><span data-stu-id="c1e0f-141">Override app configuration using the Azure Portal</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c1e0f-142">Ustawienia aplikacji w witrynie Azure Portal umożliwiają ustawianie zmiennych środowiskowych dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-142">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="c1e0f-143">Zmienne środowiskowe mogą być używane przez [dostawcę konfiguracji zmiennych środowiskowych](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="c1e0f-143">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables).</span></span>

<span data-ttu-id="c1e0f-144">Po utworzeniu lub zmodyfikowaniu ustawienia aplikacji w witrynie Azure Portal i wybraniu przycisku **Zapisz** aplikacja platformy Azure zostanie uruchomiona ponownie.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-144">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="c1e0f-145">Zmienna środowiskowa jest dostępna dla aplikacji po ponownym uruchomieniu usługi.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-145">The environment variable is available to the app after the service restarts.</span></span>

<span data-ttu-id="c1e0f-146">Gdy aplikacja korzysta z [hosta ogólnego](xref:fundamentals/host/generic-host), zmienne środowiskowe są ładowane do konfiguracji aplikacji, gdy <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> jest wywoływana w celu skompilowania hosta.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-146">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables are loaded into the app's configuration when <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="c1e0f-147">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host> i [dostawca konfiguracji zmiennych środowiskowych](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="c1e0f-147">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables).</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c1e0f-148">Ustawienia aplikacji w witrynie Azure Portal umożliwiają ustawianie zmiennych środowiskowych dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-148">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="c1e0f-149">Zmienne środowiskowe mogą być używane przez [dostawcę konfiguracji zmiennych środowiskowych](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="c1e0f-149">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="c1e0f-150">Po utworzeniu lub zmodyfikowaniu ustawienia aplikacji w witrynie Azure Portal i wybraniu przycisku **Zapisz** aplikacja platformy Azure zostanie uruchomiona ponownie.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-150">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="c1e0f-151">Zmienna środowiskowa jest dostępna dla aplikacji po ponownym uruchomieniu usługi.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-151">The environment variable is available to the app after the service restarts.</span></span>

<span data-ttu-id="c1e0f-152">Gdy aplikacja korzysta z [hosta sieci Web](xref:fundamentals/host/web-host), zmienne środowiskowe są ładowane do konfiguracji aplikacji, gdy <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> jest wywoływana w celu skompilowania hosta.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-152">When an app uses the [Web Host](xref:fundamentals/host/web-host), environment variables are loaded into the app's configuration when <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="c1e0f-153">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host> i [dostawca konfiguracji zmiennych środowiskowych](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="c1e0f-153">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="c1e0f-154">Scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="c1e0f-154">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="c1e0f-155">[Oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), które konfiguruje przekierowane nagłówki oprogramowania pośredniczącego podczas hostingu [poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), a moduł ASP.NET Core jest skonfigurowany do przesyłania dalej schematu (http/https) i zdalnego adresu IP, z którego pochodzi żądanie.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-155">The [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), which configures Forwarded Headers Middleware when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="c1e0f-156">W przypadku aplikacji hostowanych za dodatkowymi serwerami proxy i modułami równoważenia obciążenia może być wymagana dodatkowa konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-156">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="c1e0f-157">Aby uzyskać więcej informacji, zobacz [konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="c1e0f-157">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="c1e0f-158">Monitorowanie i rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="c1e0f-158">Monitoring and logging</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c1e0f-159">ASP.NET Core aplikacje wdrożone do App Service automatycznie otrzymują rozszerzenie App Service **ASP.NET Core integrację rejestrowania**.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-159">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Integration**.</span></span> <span data-ttu-id="c1e0f-160">Rozszerzenie włącza integrację rejestrowania dla aplikacji ASP.NET Core w Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-160">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c1e0f-161">ASP.NET Core aplikacje wdrożone do App Service automatycznie otrzymują rozszerzenia App Service **ASP.NET Core rejestrowania rozszerzeń**.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-161">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Extensions**.</span></span> <span data-ttu-id="c1e0f-162">Rozszerzenie włącza integrację rejestrowania dla aplikacji ASP.NET Core w Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-162">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

<span data-ttu-id="c1e0f-163">Informacje o monitorowaniu, rejestrowaniu i rozwiązywaniu problemów znajdują się w następujących artykułach:</span><span class="sxs-lookup"><span data-stu-id="c1e0f-163">For monitoring, logging, and troubleshooting information, see the following articles:</span></span>

[<span data-ttu-id="c1e0f-164">Monitorowanie aplikacji w Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c1e0f-164">Monitor apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)  
<span data-ttu-id="c1e0f-165">Dowiedz się, jak przeglądać limity przydziału i metryki dla aplikacji i planów App Service.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-165">Learn how to review quotas and metrics for apps and App Service plans.</span></span>

[<span data-ttu-id="c1e0f-166">Włączanie rejestrowania diagnostycznego dla aplikacji w Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c1e0f-166">Enable diagnostics logging for apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)  
<span data-ttu-id="c1e0f-167">Odkryj, jak włączyć i uzyskać dostęp do rejestrowania diagnostycznego dla kodów stanu HTTP, żądań zakończonych niepowodzeniem i aktywności serwera sieci Web.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-167">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="c1e0f-168">Poznaj typowe podejścia do obsługi błędów w aplikacjach ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-168">Understand common approaches to handling errors in ASP.NET Core apps.</span></span>

<xref:test/troubleshoot-azure-iis>  
<span data-ttu-id="c1e0f-169">Dowiedz się, jak zdiagnozować problemy z wdrożeniami Azure App Service przy użyciu aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-169">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-iis-errors-reference>  
<span data-ttu-id="c1e0f-170">Zapoznaj się z informacjami o typowych problemach z konfiguracją wdrażania dla aplikacji hostowanych przez Azure App Service/usług IIS.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-170">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span></span>

## <a name="data-protection-key-ring-and-deployment-slots"></a><span data-ttu-id="c1e0f-171">Pierścień i miejsca wdrożenia klucza ochrony danych</span><span class="sxs-lookup"><span data-stu-id="c1e0f-171">Data Protection key ring and deployment slots</span></span>

<span data-ttu-id="c1e0f-172">[Klucze ochrony danych](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) są utrwalane w folderze *%Home%\ASP.NET\DataProtection-Keys* .</span><span class="sxs-lookup"><span data-stu-id="c1e0f-172">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="c1e0f-173">Ten folder jest obsługiwany przez magazyn sieciowy i synchronizowany na wszystkich komputerach obsługujących aplikację.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-173">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span> <span data-ttu-id="c1e0f-174">Klucze nie są chronione w stanie spoczynku.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-174">Keys aren't protected at rest.</span></span> <span data-ttu-id="c1e0f-175">Ten folder dostarcza pierścień kluczy do wszystkich wystąpień aplikacji w jednym miejscu wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-175">This folder supplies the key ring to all instances of an app in a single deployment slot.</span></span> <span data-ttu-id="c1e0f-176">Oddzielne miejsca wdrożenia, takie jak przygotowanie i środowisko produkcyjne, nie dzielą się z kluczem.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-176">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span>

<span data-ttu-id="c1e0f-177">W przypadku wymiany między miejscami wdrożenia każdy system korzystający z ochrony danych nie będzie w stanie odszyfrować przechowywanych danych przy użyciu dzwonka klucza w poprzednim gnieździe.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-177">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="c1e0f-178">Oprogramowanie pośredniczące ASP.NET plików cookie używa ochrony danych do ochrony plików cookie.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-178">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span></span> <span data-ttu-id="c1e0f-179">Prowadzi to do użytkowników wylogowanych z aplikacji korzystającej ze standardowego oprogramowania ASP.NET cookie.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-179">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span></span> <span data-ttu-id="c1e0f-180">W przypadku rozwiązania z niezależnym dzwonkiem, należy użyć zewnętrznego dostawcy usługi Key dystynktywnego, takiego jak:</span><span class="sxs-lookup"><span data-stu-id="c1e0f-180">For a slot-independent key ring solution, use an external key ring provider, such as:</span></span>

* <span data-ttu-id="c1e0f-181">Azure Blob Storage</span><span class="sxs-lookup"><span data-stu-id="c1e0f-181">Azure Blob Storage</span></span>
* <span data-ttu-id="c1e0f-182">W usłudze Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="c1e0f-182">Azure Key Vault</span></span>
* <span data-ttu-id="c1e0f-183">Sklep SQL</span><span class="sxs-lookup"><span data-stu-id="c1e0f-183">SQL store</span></span>
* <span data-ttu-id="c1e0f-184">Pamięć podręczna Redis</span><span class="sxs-lookup"><span data-stu-id="c1e0f-184">Redis cache</span></span>

<span data-ttu-id="c1e0f-185">Aby uzyskać więcej informacji, zobacz <xref:security/data-protection/implementation/key-storage-providers>.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-185">For more information, see <xref:security/data-protection/implementation/key-storage-providers>.</span></span>
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-an-aspnet-core-app-that-uses-a-net-core-preview"></a><span data-ttu-id="c1e0f-186">Wdrażanie aplikacji ASP.NET Core korzystającej z platformy .NET Core Preview</span><span class="sxs-lookup"><span data-stu-id="c1e0f-186">Deploy an ASP.NET Core app that uses a .NET Core preview</span></span>

<span data-ttu-id="c1e0f-187">Aby wdrożyć aplikację, która korzysta z wersji zapoznawczej programu .NET Core, zobacz następujące zasoby.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-187">To deploy an app that uses a preview release of .NET Core, see the following resources.</span></span> <span data-ttu-id="c1e0f-188">Te podejścia są również używane, gdy środowisko uruchomieniowe jest dostępne, ale zestaw SDK nie został zainstalowany na Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-188">These approaches are also used when the runtime is available but the SDK hasn't been installed on Azure App Service.</span></span>

* [<span data-ttu-id="c1e0f-189">Określ wersję zestaw .NET Core SDK przy użyciu Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="c1e0f-189">Specify the .NET Core SDK Version using Azure Pipelines</span></span>](#specify-the-net-core-sdk-version-using-azure-pipelines)
* [<span data-ttu-id="c1e0f-190">Wdrażanie samodzielnej aplikacji w wersji zapoznawczej</span><span class="sxs-lookup"><span data-stu-id="c1e0f-190">Deploy a self-contained preview app</span></span>](#deploy-a-self-contained-preview-app)
* [<span data-ttu-id="c1e0f-191">Korzystanie z platformy Docker z Web Apps dla kontenerów</span><span class="sxs-lookup"><span data-stu-id="c1e0f-191">Use Docker with Web Apps for containers</span></span>](#use-docker-with-web-apps-for-containers)
* [<span data-ttu-id="c1e0f-192">Zainstaluj rozszerzenie witryny w wersji zapoznawczej</span><span class="sxs-lookup"><span data-stu-id="c1e0f-192">Install the preview site extension</span></span>](#install-the-preview-site-extension)

<span data-ttu-id="c1e0f-193">Zapoznaj się z wersją ASP.NET Core dostępną w usłudze Azure App Service, [ASP.NET Core na pulpicie nawigacyjnym app Service](https://aspnetcoreon.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="c1e0f-193">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a><span data-ttu-id="c1e0f-194">Określ wersję zestaw .NET Core SDK przy użyciu Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="c1e0f-194">Specify the .NET Core SDK Version using Azure Pipelines</span></span>

<span data-ttu-id="c1e0f-195">Użyj [Azure App Service scenariuszy Ci/CD](/azure/app-service/deploy-continuous-deployment) , aby skonfigurować kompilację ciągłej integracji za pomocą usługi Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-195">Use [Azure App Service CI/CD scenarios](/azure/app-service/deploy-continuous-deployment) to set up a continuous integration build with Azure DevOps.</span></span> <span data-ttu-id="c1e0f-196">Po utworzeniu kompilacji usługi Azure DevOps, opcjonalnie Skonfiguruj kompilację do korzystania z określonej wersji zestawu SDK.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-196">After the Azure DevOps build is created, optionally configure the build to use a specific SDK version.</span></span> 

#### <a name="specify-the-net-core-sdk-version"></a><span data-ttu-id="c1e0f-197">Określ wersję zestaw .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="c1e0f-197">Specify the .NET Core SDK version</span></span>

<span data-ttu-id="c1e0f-198">Korzystając z centrum wdrażania App Service, aby utworzyć kompilację usługi Azure DevOps, domyślny potok kompilacji zawiera kroki dla `Restore` , `Build` , `Test` i `Publish` .</span><span class="sxs-lookup"><span data-stu-id="c1e0f-198">When using the App Service deployment center to create an Azure DevOps build, the default build pipeline includes steps for `Restore`, `Build`, `Test`, and `Publish`.</span></span> <span data-ttu-id="c1e0f-199">Aby określić wersję zestawu SDK, wybierz przycisk **Dodaj (+)** na liście zadań agenta, aby dodać nowy krok.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-199">To specify the SDK version, select the **Add (+)** button in the Agent job list to add a new step.</span></span> <span data-ttu-id="c1e0f-200">Wyszukaj **zestaw .NET Core SDK** na pasku wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-200">Search for **.NET Core SDK** in the search bar.</span></span> 

![Dodaj krok zestaw .NET Core SDK](index/add-sdk-step.png)

<span data-ttu-id="c1e0f-202">Przenieś krok do pierwszej pozycji w kompilacji, aby wykonać kroki opisane w tej wersji zestaw .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-202">Move the step into the first position in the build so that the steps following it use the specified version of the .NET Core SDK.</span></span> <span data-ttu-id="c1e0f-203">Określ wersję zestaw .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-203">Specify the version of the .NET Core SDK.</span></span> <span data-ttu-id="c1e0f-204">W tym przykładzie zestaw SDK jest ustawiony na `3.0.100` .</span><span class="sxs-lookup"><span data-stu-id="c1e0f-204">In this example, the SDK is set to `3.0.100`.</span></span>

![Ukończony krok zestawu SDK](index/sdk-step-first-place.png)

<span data-ttu-id="c1e0f-206">Aby opublikować [wdrożenie samodzielne (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), skonfiguruj SCD w tym `Publish` kroku i podaj [Identyfikator czasu wykonywania (RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="c1e0f-206">To publish a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), configure SCD in the `Publish` step and provide the [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

![Samodzielna publikacja](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a><span data-ttu-id="c1e0f-208">Wdrażanie samodzielnej aplikacji w wersji zapoznawczej</span><span class="sxs-lookup"><span data-stu-id="c1e0f-208">Deploy a self-contained preview app</span></span>

<span data-ttu-id="c1e0f-209">[Wdrożenie samodzielne (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) , które jest przeznaczone dla środowiska uruchomieniowego w wersji zapoznawczej, przenosi środowisko uruchomieniowe w wersji zapoznawczej w ramach wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-209">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span></span>

<span data-ttu-id="c1e0f-210">Podczas wdrażania aplikacji samodzielnej:</span><span class="sxs-lookup"><span data-stu-id="c1e0f-210">When deploying a self-contained app:</span></span>

* <span data-ttu-id="c1e0f-211">Lokacja w Azure App Service nie wymaga [rozszerzenia witryny w wersji zapoznawczej](#install-the-preview-site-extension).</span><span class="sxs-lookup"><span data-stu-id="c1e0f-211">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="c1e0f-212">Aplikacja musi zostać opublikowana przy użyciu innego podejścia niż w przypadku publikowania dla [wdrożenia zależnego od platformy (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="c1e0f-212">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="c1e0f-213">Postępuj zgodnie ze wskazówkami [zawartymi w sekcji Wdróż aplikację samodzielną](#deploy-the-app-self-contained) .</span><span class="sxs-lookup"><span data-stu-id="c1e0f-213">Follow the guidance in the [Deploy the app self-contained](#deploy-the-app-self-contained) section.</span></span>

### <a name="use-docker-with-web-apps-for-containers"></a><span data-ttu-id="c1e0f-214">Korzystanie z platformy Docker z Web Apps dla kontenerów</span><span class="sxs-lookup"><span data-stu-id="c1e0f-214">Use Docker with Web Apps for containers</span></span>

<span data-ttu-id="c1e0f-215">[Centrum platformy Docker](https://hub.docker.com/r/microsoft/aspnetcore/) zawiera najnowsze obrazy platformy Docker w wersji zapoznawczej.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-215">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span></span> <span data-ttu-id="c1e0f-216">Obrazy mogą być używane jako obraz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-216">The images can be used as a base image.</span></span> <span data-ttu-id="c1e0f-217">Użyj obrazu i Wdróż go w celu zapewnienia normalnej Web Apps kontenerów.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-217">Use the image and deploy to Web Apps for Containers normally.</span></span>

### <a name="install-the-preview-site-extension"></a><span data-ttu-id="c1e0f-218">Zainstaluj rozszerzenie witryny w wersji zapoznawczej</span><span class="sxs-lookup"><span data-stu-id="c1e0f-218">Install the preview site extension</span></span>

<span data-ttu-id="c1e0f-219">Jeśli wystąpi problem przy użyciu rozszerzenia witryny w wersji zapoznawczej, Otwórz problem z poleceniem [dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/issues).</span><span class="sxs-lookup"><span data-stu-id="c1e0f-219">If a problem occurs using the preview site extension, open an [dotnet/AspNetCore issue](https://github.com/dotnet/AspNetCore/issues).</span></span>

1. <span data-ttu-id="c1e0f-220">W witrynie Azure Portal przejdź do App Service.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-220">From the Azure Portal, navigate to the App Service.</span></span>
1. <span data-ttu-id="c1e0f-221">Wybierz aplikację sieci Web.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-221">Select the web app.</span></span>
1. <span data-ttu-id="c1e0f-222">Wpisz "ex" w polu wyszukiwania, aby odfiltrować "rozszerzenia", lub przewiń w dół listy narzędzi do zarządzania.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-222">Type "ex" in the search box to filter for "Extensions" or scroll down the list of management tools.</span></span>
1. <span data-ttu-id="c1e0f-223">Wybierz pozycję **Rozszerzenia**.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-223">Select **Extensions**.</span></span>
1. <span data-ttu-id="c1e0f-224">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-224">Select **Add**.</span></span>
1. <span data-ttu-id="c1e0f-225">ASP.NET Core wybierz z listy rozszerzenie **środowiska uruchomieniowego {X. Y} ({x64 | x86})** , na którym `{X.Y}` jest ASP.NET Core wersja zapoznawcza i `{x64|x86}` Określa platformę.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-225">Select the **ASP.NET Core {X.Y} ({x64|x86}) Runtime** extension from the list, where `{X.Y}` is the ASP.NET Core preview version and `{x64|x86}` specifies the platform.</span></span>
1. <span data-ttu-id="c1e0f-226">Wybierz **przycisk OK** , aby zaakceptować postanowienia prawne.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-226">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="c1e0f-227">Wybierz **przycisk OK** , aby zainstalować rozszerzenie.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-227">Select **OK** to install the extension.</span></span>

<span data-ttu-id="c1e0f-228">Po zakończeniu operacji zostanie zainstalowana najnowsza wersja programu .NET Core Preview.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-228">When the operation completes, the latest .NET Core preview is installed.</span></span> <span data-ttu-id="c1e0f-229">Sprawdź instalację:</span><span class="sxs-lookup"><span data-stu-id="c1e0f-229">Verify the installation:</span></span>

1. <span data-ttu-id="c1e0f-230">Wybierz pozycję **Narzędzia zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-230">Select **Advanced Tools**.</span></span>
1. <span data-ttu-id="c1e0f-231">Wybierz pozycję **Przejdź** do **zaawansowanych narzędzi**.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-231">Select **Go** in **Advanced Tools**.</span></span>
1. <span data-ttu-id="c1e0f-232">Wybierz **Debug console**  >  element menu**programu PowerShell** konsoli debugowania.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-232">Select the **Debug console** > **PowerShell** menu item.</span></span>
1. <span data-ttu-id="c1e0f-233">W wierszu polecenia programu PowerShell wykonaj następujące polecenie.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-233">At the PowerShell prompt, execute the following command.</span></span> <span data-ttu-id="c1e0f-234">Zastąp wersję środowiska uruchomieniowego ASP.NET Core dla programu `{X.Y}` i platformy dla `{PLATFORM}` programu w ramach polecenia:</span><span class="sxs-lookup"><span data-stu-id="c1e0f-234">Substitute the ASP.NET Core runtime version for `{X.Y}` and the platform for `{PLATFORM}` in the command:</span></span>

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   <span data-ttu-id="c1e0f-235">Polecenie zwraca wartość `True` , gdy jest zainstalowane środowisko uruchomieniowe x64 w wersji zapoznawczej.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-235">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="c1e0f-236">Architektura platformy (x86/x64) aplikacji App Services jest ustawiana w ustawieniach aplikacji w witrynie Azure Portal dla aplikacji hostowanych w warstwie obliczeniowej serii A (podstawowa) lub wyższej.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-236">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="c1e0f-237">Upewnij się, że ustawienia publikowania aplikacji (na przykład w profilu publikacji programu Visual Studio [(. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) są zgodne z ustawieniami w konfiguracji usługi aplikacji w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-237">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure portal.</span></span>
>
> <span data-ttu-id="c1e0f-238">Jeśli aplikacja jest uruchamiana w trybie w procesie i architektura platformy jest skonfigurowana pod kątem 64-bitowej (x64), moduł ASP.NET Core używa 64-bitowego środowiska uruchomieniowego w wersji zapoznawczej, jeśli jest obecny.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-238">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span></span> <span data-ttu-id="c1e0f-239">Zainstaluj rozszerzenie **uruchomieniowe ASP.NET Core {X. Y} (x64)** przy użyciu witryny Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-239">Install the **ASP.NET Core {X.Y} (x64) Runtime** extension using the Azure Portal.</span></span>
>
> <span data-ttu-id="c1e0f-240">Po zainstalowaniu środowiska uruchomieniowego w wersji zapoznawczej x64 Uruchom następujące polecenie w oknie poleceń programu PowerShell platformy Azure kudu, aby zweryfikować instalację.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-240">After installing the x64 preview runtime, run the following command in the Azure Kudu PowerShell command window to verify the installation.</span></span> <span data-ttu-id="c1e0f-241">Zastąp ASP.NET Core wersję środowiska uruchomieniowego `{X.Y}` w następującym poleceniu:</span><span class="sxs-lookup"><span data-stu-id="c1e0f-241">Substitute the ASP.NET Core runtime version for `{X.Y}` in the following command:</span></span>
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> <span data-ttu-id="c1e0f-242">Polecenie zwraca wartość `True` , gdy jest zainstalowane środowisko uruchomieniowe x64 w wersji zapoznawczej.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-242">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="c1e0f-243">**Rozszerzenia ASP.NET Core** umożliwiają korzystanie z dodatkowych funkcji ASP.NET Core na platformie Azure App Services, takich jak Włączanie rejestrowania na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-243">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span></span> <span data-ttu-id="c1e0f-244">Rozszerzenie jest instalowane automatycznie podczas wdrażania z programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-244">The extension is installed automatically when deploying from Visual Studio.</span></span> <span data-ttu-id="c1e0f-245">Jeśli rozszerzenie nie jest zainstalowane, zainstaluj je dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-245">If the extension isn't installed, install it for the app.</span></span>

<span data-ttu-id="c1e0f-246">**Używanie rozszerzenia witryny w wersji zapoznawczej z szablonem ARM**</span><span class="sxs-lookup"><span data-stu-id="c1e0f-246">**Use the preview site extension with an ARM template**</span></span>

<span data-ttu-id="c1e0f-247">Jeśli szablon ARM jest używany do tworzenia i wdrażania aplikacji, `siteextensions` Typ zasobu może służyć do dodawania rozszerzenia witryny do aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-247">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span></span> <span data-ttu-id="c1e0f-248">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="c1e0f-248">For example:</span></span>

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="c1e0f-249">Publikowanie i wdrażanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="c1e0f-249">Publish and deploy the app</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c1e0f-250">W przypadku wdrożenia 64-bitowego:</span><span class="sxs-lookup"><span data-stu-id="c1e0f-250">For a 64-bit deployment:</span></span>

* <span data-ttu-id="c1e0f-251">Aby utworzyć aplikację 64-bitową, użyj 64-bitowej zestaw .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-251">Use a 64-bit .NET Core SDK to build a 64-bit app.</span></span>
* <span data-ttu-id="c1e0f-252">Ustaw **platformę** na **64 bit** w **Configuration**  >  **ustawieniach ogólnych**konfiguracji App Service.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-252">Set the **Platform** to **64 Bit** in the App Service's **Configuration** > **General settings**.</span></span> <span data-ttu-id="c1e0f-253">Aby umożliwić wybór liczby bitów platformy, aplikacja musi korzystać z planu usługi w warstwie Podstawowa lub wyższa.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-253">The app must use a Basic or higher service plan to enable the choice of platform bitness.</span></span>

::: moniker-end

### <a name="deploy-the-app-framework-dependent"></a><span data-ttu-id="c1e0f-254">Wdróż aplikację zależną od platformy</span><span class="sxs-lookup"><span data-stu-id="c1e0f-254">Deploy the app framework-dependent</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1e0f-255">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1e0f-255">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="c1e0f-256">Wybierz pozycję **kompilacja**  >  **Opublikuj {nazwa aplikacji}** na pasku narzędzi programu Visual Studio lub kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **Publikuj**.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-256">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="c1e0f-257">W oknie dialogowym **Wybieranie elementu docelowego publikowania** upewnij się, że **App Service** jest zaznaczone.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-257">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="c1e0f-258">Wybierz pozycję **Zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-258">Select **Advanced**.</span></span> <span data-ttu-id="c1e0f-259">Zostanie otwarte okno dialogowe **Publikowanie** .</span><span class="sxs-lookup"><span data-stu-id="c1e0f-259">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="c1e0f-260">W oknie dialogowym **publikowania** :</span><span class="sxs-lookup"><span data-stu-id="c1e0f-260">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="c1e0f-261">Upewnij się, że wybrano konfigurację **wydania** .</span><span class="sxs-lookup"><span data-stu-id="c1e0f-261">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="c1e0f-262">Otwórz listę rozwijaną **tryb wdrażania** i wybierz pozycję **zależne od struktury**.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-262">Open the **Deployment Mode** drop-down list and select **Framework-Dependent**.</span></span>
   * <span data-ttu-id="c1e0f-263">Wybierz **przenośne** jako **docelowe środowisko uruchomieniowe**.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-263">Select **Portable** as the **Target Runtime**.</span></span>
   * <span data-ttu-id="c1e0f-264">Jeśli konieczne jest usunięcie dodatkowych plików po wdrożeniu, Otwórz **Opcje publikowania plików** i zaznacz pole wyboru w celu usunięcia dodatkowych plików w miejscu docelowym.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-264">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="c1e0f-265">Wybierz pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-265">Select **Save**.</span></span>
1. <span data-ttu-id="c1e0f-266">Utwórz nową lokację lub zaktualizuj istniejącą witrynę, postępując zgodnie z pozostałymi instrukcjami Kreatora publikacji.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-266">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c1e0f-267">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="c1e0f-267">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="c1e0f-268">W pliku projektu nie określaj [identyfikatora środowiska uruchomieniowego (RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="c1e0f-268">In the project file, don't specify a [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

1. <span data-ttu-id="c1e0f-269">W powłoce poleceń Opublikuj aplikację w konfiguracji wydania przy użyciu polecenia [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="c1e0f-269">From a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="c1e0f-270">W poniższym przykładzie aplikacja jest publikowana jako aplikacja zależna od platformy:</span><span class="sxs-lookup"><span data-stu-id="c1e0f-270">In the following example, the app is published as a framework-dependent app:</span></span>

   ```console
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="c1e0f-271">Przenieś zawartość katalogu *bin/Release/{Target Framework}/Publish* do lokacji programu w App Service.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-271">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="c1e0f-272">Jeśli przeciągasz zawartość folderu *publikowania* z lokalnego dysku twardego lub udziału sieciowego bezpośrednio do App Service w konsoli [kudu](https://github.com/projectkudu/kudu/wiki) , przeciągnij pliki do `D:\home\site\wwwroot` folderu w konsoli kudu.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-272">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the [Kudu](https://github.com/projectkudu/kudu/wiki) console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

### <a name="deploy-the-app-self-contained"></a><span data-ttu-id="c1e0f-273">Wdróż aplikację samodzielną</span><span class="sxs-lookup"><span data-stu-id="c1e0f-273">Deploy the app self-contained</span></span>

<span data-ttu-id="c1e0f-274">Użyj programu Visual Studio lub interfejs wiersza polecenia platformy .NET Core dla [wdrożenia samodzielnego (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="c1e0f-274">Use Visual Studio or the .NET Core CLI for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1e0f-275">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1e0f-275">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="c1e0f-276">Wybierz pozycję **kompilacja**  >  **Opublikuj {nazwa aplikacji}** na pasku narzędzi programu Visual Studio lub kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **Publikuj**.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-276">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="c1e0f-277">W oknie dialogowym **Wybieranie elementu docelowego publikowania** upewnij się, że **App Service** jest zaznaczone.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-277">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="c1e0f-278">Wybierz pozycję **Zaawansowane**.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-278">Select **Advanced**.</span></span> <span data-ttu-id="c1e0f-279">Zostanie otwarte okno dialogowe **Publikowanie** .</span><span class="sxs-lookup"><span data-stu-id="c1e0f-279">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="c1e0f-280">W oknie dialogowym **publikowania** :</span><span class="sxs-lookup"><span data-stu-id="c1e0f-280">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="c1e0f-281">Upewnij się, że wybrano konfigurację **wydania** .</span><span class="sxs-lookup"><span data-stu-id="c1e0f-281">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="c1e0f-282">Otwórz listę rozwijaną **tryb wdrażania** i wybierz pozycję **samodzielny**.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-282">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span></span>
   * <span data-ttu-id="c1e0f-283">Wybierz docelowe środowisko uruchomieniowe z listy rozwijanej **docelowy środowisko uruchomieniowe** .</span><span class="sxs-lookup"><span data-stu-id="c1e0f-283">Select the target runtime from the **Target Runtime** drop-down list.</span></span> <span data-ttu-id="c1e0f-284">Wartość domyślna to `win-x86`.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-284">The default is `win-x86`.</span></span>
   * <span data-ttu-id="c1e0f-285">Jeśli konieczne jest usunięcie dodatkowych plików po wdrożeniu, Otwórz **Opcje publikowania plików** i zaznacz pole wyboru w celu usunięcia dodatkowych plików w miejscu docelowym.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-285">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="c1e0f-286">Wybierz pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-286">Select **Save**.</span></span>
1. <span data-ttu-id="c1e0f-287">Utwórz nową lokację lub zaktualizuj istniejącą witrynę, postępując zgodnie z pozostałymi instrukcjami Kreatora publikacji.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-287">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c1e0f-288">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="c1e0f-288">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="c1e0f-289">W pliku projektu Określ jeden lub więcej [identyfikatorów środowiska uruchomieniowego (RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="c1e0f-289">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="c1e0f-290">Użyj `<RuntimeIdentifier>` (pojedyncze) dla pojedynczego identyfikatora RID lub Użyj `<RuntimeIdentifiers>` (plural), aby podać listę identyfikatorów RID rozdzielonych średnikami.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-290">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span></span> <span data-ttu-id="c1e0f-291">W poniższym przykładzie `win-x86` określono identyfikator RID:</span><span class="sxs-lookup"><span data-stu-id="c1e0f-291">In the following example, the `win-x86` RID is specified:</span></span>

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. <span data-ttu-id="c1e0f-292">W powłoce poleceń Opublikuj aplikację w konfiguracji wydania dla środowiska uruchomieniowego hosta za pomocą polecenia [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="c1e0f-292">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="c1e0f-293">W poniższym przykładzie aplikacja jest publikowana dla `win-x86` identyfikatora RID.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-293">In the following example, the app is published for the `win-x86` RID.</span></span> <span data-ttu-id="c1e0f-294">Identyfikator RID dostarczony do `--runtime` opcji musi być podany we `<RuntimeIdentifier>` właściwości (lub `<RuntimeIdentifiers>` ) w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-294">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span></span>

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. <span data-ttu-id="c1e0f-295">Przenieś zawartość katalogu *bin/Release/{Target Framework}/{Runtime identyfikator}/Publish* do lokacji w App Service.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-295">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="c1e0f-296">Jeśli przeciągasz zawartość folderu *publikowania* z lokalnego dysku twardego lub udziału sieciowego bezpośrednio do App Service w konsoli kudu, przeciągnij pliki do `D:\home\site\wwwroot` folderu w konsoli kudu.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-296">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the Kudu console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

## <a name="protocol-settings-https"></a><span data-ttu-id="c1e0f-297">Ustawienia protokołu (HTTPS)</span><span class="sxs-lookup"><span data-stu-id="c1e0f-297">Protocol settings (HTTPS)</span></span>

<span data-ttu-id="c1e0f-298">Bezpieczne powiązania protokołów pozwalają określić certyfikat, który ma być używany podczas odpowiadania na żądania za pośrednictwem protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-298">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span></span> <span data-ttu-id="c1e0f-299">Powiązanie wymaga prawidłowego certyfikatu prywatnego (*PFX*) dla określonej nazwy hosta.</span><span class="sxs-lookup"><span data-stu-id="c1e0f-299">Binding requires a valid private certificate (*.pfx*) issued for the specific hostname.</span></span> <span data-ttu-id="c1e0f-300">Aby uzyskać więcej informacji, zobacz [Samouczek: powiązanie istniejącego niestandardowego certyfikatu protokołu SSL z Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="c1e0f-300">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="c1e0f-301">Przekształcanie pliku web.config</span><span class="sxs-lookup"><span data-stu-id="c1e0f-301">Transform web.config</span></span>

<span data-ttu-id="c1e0f-302">Jeśli musisz przekształcić *web.config* przy publikowaniu (na przykład ustawić zmienne środowiskowe na podstawie konfiguracji, profilu lub środowiska), zobacz <xref:host-and-deploy/iis/transform-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="c1e0f-302">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c1e0f-303">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="c1e0f-303">Additional resources</span></span>

* [<span data-ttu-id="c1e0f-304">Omówienie usługi App Service</span><span class="sxs-lookup"><span data-stu-id="c1e0f-304">App Service overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="c1e0f-305">Azure App Service: najlepsze miejsce do hostowania aplikacji .NET (wideo z omówieniem 55 minut)</span><span class="sxs-lookup"><span data-stu-id="c1e0f-305">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span></span>](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [<span data-ttu-id="c1e0f-306">Piątek Azure: Azure App Service środowisko diagnostyczne i rozwiązywania problemów (wideo 12-minutowy)</span><span class="sxs-lookup"><span data-stu-id="c1e0f-306">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [<span data-ttu-id="c1e0f-307">Omówienie diagnostyki Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c1e0f-307">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

<span data-ttu-id="c1e0f-308">Azure App Service w systemie Windows Server używa [Internet Information Services (IIS)](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="c1e0f-308">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span></span> <span data-ttu-id="c1e0f-309">Poniższe tematy dotyczą podstawowej technologii usług IIS:</span><span class="sxs-lookup"><span data-stu-id="c1e0f-309">The following topics pertain to the underlying IIS technology:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [<span data-ttu-id="c1e0f-310">System Windows Server — zawartość administratora IT dla bieżących i poprzednich wersji</span><span class="sxs-lookup"><span data-stu-id="c1e0f-310">Windows Server - IT administrator content for current and previous releases</span></span>](/windows-server/windows-server-versions)
