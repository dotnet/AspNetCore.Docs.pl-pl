---
title: Hostowanie i wdrażanie ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak konfigurować środowiska hostingu i wdrażać ASP.NET Core aplikacje.
monikerRange: '>= aspnetcore-2.1'
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
uid: host-and-deploy/index
ms.openlocfilehash: 05d04a9c95910c805ea28578aba21a0658dd779a
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252971"
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="e4674-103">Hostowanie i wdrażanie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e4674-103">Host and deploy ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="e4674-104">Ogólnie rzecz biorąc, aby wdrożyć aplikację ASP.NET Core w środowisku hostingu:</span><span class="sxs-lookup"><span data-stu-id="e4674-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="e4674-105">Wdróż opublikowaną aplikację w folderze na serwerze hostingu.</span><span class="sxs-lookup"><span data-stu-id="e4674-105">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="e4674-106">Skonfiguruj Menedżera procesów uruchamiający aplikację po nadejściu żądań i ponownym uruchomieniu aplikacji po awarii lub ponownym uruchomieniu serwera.</span><span class="sxs-lookup"><span data-stu-id="e4674-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="e4674-107">W przypadku konfiguracji zwrotnego serwera proxy Skonfiguruj zwrotny serwer proxy do przesyłania dalej żądań do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e4674-107">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="e4674-108">Publikowanie w folderze</span><span class="sxs-lookup"><span data-stu-id="e4674-108">Publish to a folder</span></span>

<span data-ttu-id="e4674-109">[Dotnet Publish](/dotnet/core/tools/dotnet-publish) polecenie kompiluje kod aplikacji i kopiuje pliki wymagane do uruchomienia aplikacji w folderze *publikacji* .</span><span class="sxs-lookup"><span data-stu-id="e4674-109">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="e4674-110">Podczas wdrażania z programu Visual Studio, ten `dotnet publish` krok odbywa się automatycznie przed skopiowaniem plików do miejsca docelowego wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="e4674-110">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

## <a name="publish-settings-files"></a><span data-ttu-id="e4674-111">Publikowanie plików ustawień</span><span class="sxs-lookup"><span data-stu-id="e4674-111">Publish settings files</span></span>

<span data-ttu-id="e4674-112">`*.json` pliki są domyślnie publikowane.</span><span class="sxs-lookup"><span data-stu-id="e4674-112">`*.json` files are published by default.</span></span> <span data-ttu-id="e4674-113">Aby opublikować inne pliki ustawień, określ je w [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) elemencie w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="e4674-113">To publish other settings files, specify them in an [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) element in the project file.</span></span> <span data-ttu-id="e4674-114">Poniższy przykład publikuje pliki XML:</span><span class="sxs-lookup"><span data-stu-id="e4674-114">The following example publishes XML files:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.xml" Exclude="bin\**\*;obj\**\*"
    CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

### <a name="folder-contents"></a><span data-ttu-id="e4674-115">Zawartość folderu</span><span class="sxs-lookup"><span data-stu-id="e4674-115">Folder contents</span></span>

<span data-ttu-id="e4674-116">Folder *publikowania* zawiera jeden lub więcej plików zestawów aplikacji, zależności i opcjonalnie środowiska uruchomieniowego platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="e4674-116">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="e4674-117">Aplikację platformy .NET Core można opublikować jako *samodzielne wdrożenie* lub *wdrożenie zależne od platformy*.</span><span class="sxs-lookup"><span data-stu-id="e4674-117">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="e4674-118">Jeśli aplikacja jest samodzielna, pliki zestawu, które zawierają środowisko uruchomieniowe platformy .NET, znajdują się w folderze *Publikowanie* .</span><span class="sxs-lookup"><span data-stu-id="e4674-118">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="e4674-119">Jeśli aplikacja jest zależna od struktury, pliki środowiska uruchomieniowego platformy .NET nie są uwzględniane, ponieważ aplikacja ma odwołanie do wersji platformy .NET zainstalowanej na serwerze.</span><span class="sxs-lookup"><span data-stu-id="e4674-119">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="e4674-120">Domyślny model wdrażania jest zależny od platformy.</span><span class="sxs-lookup"><span data-stu-id="e4674-120">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="e4674-121">Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="e4674-121">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="e4674-122">Oprócz plików *exe* i *dll* , folder *publikacji* aplikacji ASP.NET Core zwykle zawiera pliki konfiguracji, zasoby statyczne i widoki MVC.</span><span class="sxs-lookup"><span data-stu-id="e4674-122">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="e4674-123">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="e4674-123">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="e4674-124">Konfigurowanie Menedżera procesów</span><span class="sxs-lookup"><span data-stu-id="e4674-124">Set up a process manager</span></span>

<span data-ttu-id="e4674-125">Aplikacja ASP.NET Core jest aplikacją konsolową, która musi zostać uruchomiona w przypadku awarii i ponownego uruchomienia serwera.</span><span class="sxs-lookup"><span data-stu-id="e4674-125">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="e4674-126">Aby zautomatyzować uruchamianie i ponowne uruchamianie, wymagany jest Menedżer procesów.</span><span class="sxs-lookup"><span data-stu-id="e4674-126">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="e4674-127">Najbardziej typowymi menedżerami procesów dla ASP.NET Core są:</span><span class="sxs-lookup"><span data-stu-id="e4674-127">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="e4674-128">Linux</span><span class="sxs-lookup"><span data-stu-id="e4674-128">Linux</span></span>
  * [<span data-ttu-id="e4674-129">Nginx</span><span class="sxs-lookup"><span data-stu-id="e4674-129">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="e4674-130">Apache</span><span class="sxs-lookup"><span data-stu-id="e4674-130">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="e4674-131">Windows</span><span class="sxs-lookup"><span data-stu-id="e4674-131">Windows</span></span>
  * [<span data-ttu-id="e4674-132">IIS</span><span class="sxs-lookup"><span data-stu-id="e4674-132">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="e4674-133">Usługa systemu Windows</span><span class="sxs-lookup"><span data-stu-id="e4674-133">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="e4674-134">Konfigurowanie zwrotnego serwera proxy</span><span class="sxs-lookup"><span data-stu-id="e4674-134">Set up a reverse proxy</span></span>

<span data-ttu-id="e4674-135">Jeśli aplikacja używa serwera [Kestrel](xref:fundamentals/servers/kestrel) , usługi [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache)lub [IIS](xref:host-and-deploy/iis/index) mogą być używane jako zwrotny serwer proxy.</span><span class="sxs-lookup"><span data-stu-id="e4674-135">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="e4674-136">Odwrotny serwer proxy odbiera żądania HTTP z Internetu i przekazuje je do usługi Kestrel.</span><span class="sxs-lookup"><span data-stu-id="e4674-136">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

::: moniker-end 

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="e4674-137">Jest to &mdash; obsługiwana konfiguracja hostingu, która jest konfiguracją z serwerem odwrotnego serwera proxy lub bez niego &mdash; .</span><span class="sxs-lookup"><span data-stu-id="e4674-137">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="e4674-138">Aby uzyskać więcej informacji, zobacz [Kiedy używać Kestrel z zwrotnym serwerem proxy](xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="e4674-138">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy).</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"
<span data-ttu-id="e4674-139">Jest to &mdash; obsługiwana konfiguracja hostingu, która jest konfiguracją z serwerem odwrotnego serwera proxy lub bez niego &mdash; .</span><span class="sxs-lookup"><span data-stu-id="e4674-139">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="e4674-140">Aby uzyskać więcej informacji, zobacz [Kiedy używać Kestrel z zwrotnym serwerem proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="e4674-140">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e4674-141">Scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="e4674-141">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e4674-142">Dodatkowa konfiguracja może być wymagana dla aplikacji hostowanych w ramach serwerów proxy i modułów równoważenia obciążenia.</span><span class="sxs-lookup"><span data-stu-id="e4674-142">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="e4674-143">Bez dodatkowej konfiguracji aplikacja może nie mieć dostępu do schematu (HTTP/HTTPS) i zdalnego adresu IP, z którego pochodzi żądanie.</span><span class="sxs-lookup"><span data-stu-id="e4674-143">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="e4674-144">Aby uzyskać więcej informacji, zobacz [konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="e4674-144">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="e4674-145">Korzystanie z programu Visual Studio i narzędzia MSBuild do automatyzowania wdrożeń</span><span class="sxs-lookup"><span data-stu-id="e4674-145">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="e4674-146">Wdrożenie często wymaga dodatkowych zadań oprócz kopiowania danych wyjściowych z [dotnet Publish](/dotnet/core/tools/dotnet-publish) na serwer.</span><span class="sxs-lookup"><span data-stu-id="e4674-146">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="e4674-147">Na przykład dodatkowe pliki mogą być wymagane lub wykluczone z folderu *publikacji* .</span><span class="sxs-lookup"><span data-stu-id="e4674-147">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="e4674-148">Program Visual Studio używa programu [MSBuild](/visualstudio/msbuild/msbuild) do wdrażania w sieci Web, a programu MSBuild można dostosować do wykonywania wielu innych zadań podczas wdrażania.</span><span class="sxs-lookup"><span data-stu-id="e4674-148">Visual Studio uses [MSBuild](/visualstudio/msbuild/msbuild) for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="e4674-149">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/visual-studio-publish-profiles> i książka [using programu MSBuild i Team Foundation Build](http://msbuildbook.com/) .</span><span class="sxs-lookup"><span data-stu-id="e4674-149">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="e4674-150">Za pomocą [funkcji publikowania w sieci Web](xref:tutorials/publish-to-azure-webapp-using-vs) lub [wbudowanej obsługi narzędzia Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment)aplikacje można wdrażać bezpośrednio z programu Visual Studio do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="e4674-150">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="e4674-151">Azure DevOps Services obsługuje [ciągłe wdrażanie do Azure App Service](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="e4674-151">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="e4674-152">Aby uzyskać więcej informacji, zobacz [DevOps with ASP.NET Core i platformę Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="e4674-152">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="e4674-153">Publikowanie na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="e4674-153">Publish to Azure</span></span>

<span data-ttu-id="e4674-154">Zobacz, <xref:tutorials/publish-to-azure-webapp-using-vs> Aby uzyskać instrukcje dotyczące publikowania aplikacji na platformie Azure przy użyciu programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e4674-154">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="e4674-155">Dodatkowy przykład jest zapewniany przez [utworzenie aplikacji sieci web ASP.NET Core na platformie Azure](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="e4674-155">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="e4674-156">Publikowanie z MSDeploy w systemie Windows</span><span class="sxs-lookup"><span data-stu-id="e4674-156">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="e4674-157">Zobacz <xref:host-and-deploy/visual-studio-publish-profiles> , aby uzyskać instrukcje dotyczące publikowania aplikacji przy użyciu profilu publikacji programu Visual Studio, w tym z wiersza polecenia systemu Windows przy użyciu polecenia [MSBuild programu dotnet](/dotnet/core/tools/dotnet-msbuild) .</span><span class="sxs-lookup"><span data-stu-id="e4674-157">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="e4674-158">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="e4674-158">Internet Information Services (IIS)</span></span>

<span data-ttu-id="e4674-159">W przypadku wdrożeń do Internet Information Services (IIS) z konfiguracją dostarczoną przez plik *web.config* zapoznaj się z artykułami poniżej <xref:host-and-deploy/iis/index> .</span><span class="sxs-lookup"><span data-stu-id="e4674-159">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="e4674-160">Hosting w farmie internetowej</span><span class="sxs-lookup"><span data-stu-id="e4674-160">Host in a web farm</span></span>

<span data-ttu-id="e4674-161">Aby uzyskać informacje na temat konfiguracji do hostowania aplikacji ASP.NET Core w środowisku kolektywu serwerów sieci Web (na przykład wdrażania wielu wystąpień aplikacji na potrzeby skalowalności), zobacz <xref:host-and-deploy/web-farm> .</span><span class="sxs-lookup"><span data-stu-id="e4674-161">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="e4674-162">Host na platformie Docker</span><span class="sxs-lookup"><span data-stu-id="e4674-162">Host on Docker</span></span>

<span data-ttu-id="e4674-163">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="e4674-163">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="perform-health-checks"></a><span data-ttu-id="e4674-164">Przeprowadzanie kontroli kondycji</span><span class="sxs-lookup"><span data-stu-id="e4674-164">Perform health checks</span></span>

<span data-ttu-id="e4674-165">Używaj oprogramowania do sprawdzania kondycji, aby przeprowadzać kontrole kondycji aplikacji i jej zależności.</span><span class="sxs-lookup"><span data-stu-id="e4674-165">Use Health Check Middleware to perform health checks on an app and its dependencies.</span></span> <span data-ttu-id="e4674-166">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/health-checks>.</span><span class="sxs-lookup"><span data-stu-id="e4674-166">For more information, see <xref:host-and-deploy/health-checks>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e4674-167">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="e4674-167">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="e4674-168">Hosting ASP.NET</span><span class="sxs-lookup"><span data-stu-id="e4674-168">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="e4674-169">Ogólnie rzecz biorąc, aby wdrożyć aplikację ASP.NET Core w środowisku hostingu:</span><span class="sxs-lookup"><span data-stu-id="e4674-169">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="e4674-170">Wdróż opublikowaną aplikację w folderze na serwerze hostingu.</span><span class="sxs-lookup"><span data-stu-id="e4674-170">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="e4674-171">Skonfiguruj Menedżera procesów uruchamiający aplikację po nadejściu żądań i ponownym uruchomieniu aplikacji po awarii lub ponownym uruchomieniu serwera.</span><span class="sxs-lookup"><span data-stu-id="e4674-171">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="e4674-172">W przypadku konfiguracji zwrotnego serwera proxy Skonfiguruj zwrotny serwer proxy do przesyłania dalej żądań do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e4674-172">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="e4674-173">Publikowanie w folderze</span><span class="sxs-lookup"><span data-stu-id="e4674-173">Publish to a folder</span></span>

<span data-ttu-id="e4674-174">[Dotnet Publish](/dotnet/core/tools/dotnet-publish) polecenie kompiluje kod aplikacji i kopiuje pliki wymagane do uruchomienia aplikacji w folderze *publikacji* .</span><span class="sxs-lookup"><span data-stu-id="e4674-174">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="e4674-175">Podczas wdrażania z programu Visual Studio, ten `dotnet publish` krok odbywa się automatycznie przed skopiowaniem plików do miejsca docelowego wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="e4674-175">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="e4674-176">Zawartość folderu</span><span class="sxs-lookup"><span data-stu-id="e4674-176">Folder contents</span></span>

<span data-ttu-id="e4674-177">Folder *publikowania* zawiera jeden lub więcej plików zestawów aplikacji, zależności i opcjonalnie środowiska uruchomieniowego platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="e4674-177">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="e4674-178">Aplikację platformy .NET Core można opublikować jako *samodzielne wdrożenie* lub *wdrożenie zależne od platformy*.</span><span class="sxs-lookup"><span data-stu-id="e4674-178">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="e4674-179">Jeśli aplikacja jest samodzielna, pliki zestawu, które zawierają środowisko uruchomieniowe platformy .NET, znajdują się w folderze *Publikowanie* .</span><span class="sxs-lookup"><span data-stu-id="e4674-179">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="e4674-180">Jeśli aplikacja jest zależna od struktury, pliki środowiska uruchomieniowego platformy .NET nie są uwzględniane, ponieważ aplikacja ma odwołanie do wersji platformy .NET zainstalowanej na serwerze.</span><span class="sxs-lookup"><span data-stu-id="e4674-180">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="e4674-181">Domyślny model wdrażania jest zależny od platformy.</span><span class="sxs-lookup"><span data-stu-id="e4674-181">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="e4674-182">Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="e4674-182">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="e4674-183">Oprócz plików *exe* i *dll* , folder *publikacji* aplikacji ASP.NET Core zwykle zawiera pliki konfiguracji, zasoby statyczne i widoki MVC.</span><span class="sxs-lookup"><span data-stu-id="e4674-183">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="e4674-184">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="e4674-184">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="e4674-185">Konfigurowanie Menedżera procesów</span><span class="sxs-lookup"><span data-stu-id="e4674-185">Set up a process manager</span></span>

<span data-ttu-id="e4674-186">Aplikacja ASP.NET Core jest aplikacją konsolową, która musi zostać uruchomiona w przypadku awarii i ponownego uruchomienia serwera.</span><span class="sxs-lookup"><span data-stu-id="e4674-186">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="e4674-187">Aby zautomatyzować uruchamianie i ponowne uruchamianie, wymagany jest Menedżer procesów.</span><span class="sxs-lookup"><span data-stu-id="e4674-187">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="e4674-188">Najbardziej typowymi menedżerami procesów dla ASP.NET Core są:</span><span class="sxs-lookup"><span data-stu-id="e4674-188">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="e4674-189">Linux</span><span class="sxs-lookup"><span data-stu-id="e4674-189">Linux</span></span>
  * [<span data-ttu-id="e4674-190">Nginx</span><span class="sxs-lookup"><span data-stu-id="e4674-190">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="e4674-191">Apache</span><span class="sxs-lookup"><span data-stu-id="e4674-191">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="e4674-192">Windows</span><span class="sxs-lookup"><span data-stu-id="e4674-192">Windows</span></span>
  * [<span data-ttu-id="e4674-193">IIS</span><span class="sxs-lookup"><span data-stu-id="e4674-193">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="e4674-194">Usługa systemu Windows</span><span class="sxs-lookup"><span data-stu-id="e4674-194">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="e4674-195">Konfigurowanie zwrotnego serwera proxy</span><span class="sxs-lookup"><span data-stu-id="e4674-195">Set up a reverse proxy</span></span>

<span data-ttu-id="e4674-196">Jeśli aplikacja używa serwera [Kestrel](xref:fundamentals/servers/kestrel) , usługi [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache)lub [IIS](xref:host-and-deploy/iis/index) mogą być używane jako zwrotny serwer proxy.</span><span class="sxs-lookup"><span data-stu-id="e4674-196">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="e4674-197">Odwrotny serwer proxy odbiera żądania HTTP z Internetu i przekazuje je do usługi Kestrel.</span><span class="sxs-lookup"><span data-stu-id="e4674-197">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="e4674-198">Jest to &mdash; obsługiwana konfiguracja hostingu, która jest konfiguracją z serwerem odwrotnego serwera proxy lub bez niego &mdash; .</span><span class="sxs-lookup"><span data-stu-id="e4674-198">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="e4674-199">Aby uzyskać więcej informacji, zobacz [Kiedy używać Kestrel z zwrotnym serwerem proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="e4674-199">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e4674-200">Scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="e4674-200">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e4674-201">Dodatkowa konfiguracja może być wymagana dla aplikacji hostowanych w ramach serwerów proxy i modułów równoważenia obciążenia.</span><span class="sxs-lookup"><span data-stu-id="e4674-201">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="e4674-202">Bez dodatkowej konfiguracji aplikacja może nie mieć dostępu do schematu (HTTP/HTTPS) i zdalnego adresu IP, z którego pochodzi żądanie.</span><span class="sxs-lookup"><span data-stu-id="e4674-202">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="e4674-203">Aby uzyskać więcej informacji, zobacz [konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="e4674-203">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="e4674-204">Korzystanie z programu Visual Studio i narzędzia MSBuild do automatyzowania wdrożeń</span><span class="sxs-lookup"><span data-stu-id="e4674-204">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="e4674-205">Wdrożenie często wymaga dodatkowych zadań oprócz kopiowania danych wyjściowych z [dotnet Publish](/dotnet/core/tools/dotnet-publish) na serwer.</span><span class="sxs-lookup"><span data-stu-id="e4674-205">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="e4674-206">Na przykład dodatkowe pliki mogą być wymagane lub wykluczone z folderu *publikacji* .</span><span class="sxs-lookup"><span data-stu-id="e4674-206">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="e4674-207">Program Visual Studio używa programu MSBuild do wdrażania w sieci Web, a programu MSBuild można dostosować do wykonywania wielu innych zadań podczas wdrażania.</span><span class="sxs-lookup"><span data-stu-id="e4674-207">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="e4674-208">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/visual-studio-publish-profiles> i książka [using programu MSBuild i Team Foundation Build](http://msbuildbook.com/) .</span><span class="sxs-lookup"><span data-stu-id="e4674-208">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="e4674-209">Za pomocą [funkcji publikowania w sieci Web](xref:tutorials/publish-to-azure-webapp-using-vs) lub [wbudowanej obsługi narzędzia Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment)aplikacje można wdrażać bezpośrednio z programu Visual Studio do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="e4674-209">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="e4674-210">Azure DevOps Services obsługuje [ciągłe wdrażanie do Azure App Service](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="e4674-210">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="e4674-211">Aby uzyskać więcej informacji, zobacz [DevOps with ASP.NET Core i platformę Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="e4674-211">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="e4674-212">Publikowanie na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="e4674-212">Publish to Azure</span></span>

<span data-ttu-id="e4674-213">Zobacz, <xref:tutorials/publish-to-azure-webapp-using-vs> Aby uzyskać instrukcje dotyczące publikowania aplikacji na platformie Azure przy użyciu programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e4674-213">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="e4674-214">Dodatkowy przykład jest zapewniany przez [utworzenie aplikacji sieci web ASP.NET Core na platformie Azure](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="e4674-214">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="e4674-215">Publikowanie z MSDeploy w systemie Windows</span><span class="sxs-lookup"><span data-stu-id="e4674-215">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="e4674-216">Zobacz <xref:host-and-deploy/visual-studio-publish-profiles> , aby uzyskać instrukcje dotyczące publikowania aplikacji przy użyciu profilu publikacji programu Visual Studio, w tym z wiersza polecenia systemu Windows przy użyciu polecenia [MSBuild programu dotnet](/dotnet/core/tools/dotnet-msbuild) .</span><span class="sxs-lookup"><span data-stu-id="e4674-216">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="e4674-217">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="e4674-217">Internet Information Services (IIS)</span></span>

<span data-ttu-id="e4674-218">W przypadku wdrożeń do Internet Information Services (IIS) z konfiguracją dostarczoną przez plik *web.config* zapoznaj się z artykułami poniżej <xref:host-and-deploy/iis/index> .</span><span class="sxs-lookup"><span data-stu-id="e4674-218">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="e4674-219">Hosting w farmie internetowej</span><span class="sxs-lookup"><span data-stu-id="e4674-219">Host in a web farm</span></span>

<span data-ttu-id="e4674-220">Aby uzyskać informacje na temat konfiguracji do hostowania aplikacji ASP.NET Core w środowisku kolektywu serwerów sieci Web (na przykład wdrażania wielu wystąpień aplikacji na potrzeby skalowalności), zobacz <xref:host-and-deploy/web-farm> .</span><span class="sxs-lookup"><span data-stu-id="e4674-220">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="e4674-221">Host na platformie Docker</span><span class="sxs-lookup"><span data-stu-id="e4674-221">Host on Docker</span></span>

<span data-ttu-id="e4674-222">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="e4674-222">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e4674-223">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="e4674-223">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="e4674-224">Hosting ASP.NET</span><span class="sxs-lookup"><span data-stu-id="e4674-224">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
