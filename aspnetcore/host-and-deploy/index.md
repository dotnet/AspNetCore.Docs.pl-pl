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
ms.openlocfilehash: 4f3d4c29a189cf6aa14eb10f570f0b35d8ff9abc
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556622"
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="a4a41-103">Hostowanie i wdrażanie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a4a41-103">Host and deploy ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="a4a41-104">Ogólnie rzecz biorąc, aby wdrożyć aplikację ASP.NET Core w środowisku hostingu:</span><span class="sxs-lookup"><span data-stu-id="a4a41-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="a4a41-105">Wdróż opublikowaną aplikację w folderze na serwerze hostingu.</span><span class="sxs-lookup"><span data-stu-id="a4a41-105">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="a4a41-106">Skonfiguruj Menedżera procesów uruchamiający aplikację po nadejściu żądań i ponownym uruchomieniu aplikacji po awarii lub ponownym uruchomieniu serwera.</span><span class="sxs-lookup"><span data-stu-id="a4a41-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="a4a41-107">W przypadku konfiguracji zwrotnego serwera proxy Skonfiguruj zwrotny serwer proxy do przesyłania dalej żądań do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4a41-107">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="a4a41-108">Publikowanie w folderze</span><span class="sxs-lookup"><span data-stu-id="a4a41-108">Publish to a folder</span></span>

<span data-ttu-id="a4a41-109">[Dotnet Publish](/dotnet/core/tools/dotnet-publish) polecenie kompiluje kod aplikacji i kopiuje pliki wymagane do uruchomienia aplikacji w folderze *publikacji* .</span><span class="sxs-lookup"><span data-stu-id="a4a41-109">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="a4a41-110">Podczas wdrażania z programu Visual Studio, ten `dotnet publish` krok odbywa się automatycznie przed skopiowaniem plików do miejsca docelowego wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="a4a41-110">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

## <a name="publish-settings-files"></a><span data-ttu-id="a4a41-111">Publikowanie plików ustawień</span><span class="sxs-lookup"><span data-stu-id="a4a41-111">Publish settings files</span></span>

<span data-ttu-id="a4a41-112">`*.json` pliki są domyślnie publikowane.</span><span class="sxs-lookup"><span data-stu-id="a4a41-112">`*.json` files are published by default.</span></span> <span data-ttu-id="a4a41-113">Aby opublikować inne pliki ustawień, określ je w [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) elemencie w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="a4a41-113">To publish other settings files, specify them in an [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) element in the project file.</span></span> <span data-ttu-id="a4a41-114">Poniższy przykład publikuje pliki XML:</span><span class="sxs-lookup"><span data-stu-id="a4a41-114">The following example publishes XML files:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.xml" Exclude="bin\**\*;obj\**\*"
    CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

### <a name="folder-contents"></a><span data-ttu-id="a4a41-115">Zawartość folderu</span><span class="sxs-lookup"><span data-stu-id="a4a41-115">Folder contents</span></span>

<span data-ttu-id="a4a41-116">Folder *publikowania* zawiera jeden lub więcej plików zestawów aplikacji, zależności i opcjonalnie środowiska uruchomieniowego platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="a4a41-116">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="a4a41-117">Aplikację platformy .NET Core można opublikować jako *samodzielne wdrożenie* lub *wdrożenie zależne od platformy*.</span><span class="sxs-lookup"><span data-stu-id="a4a41-117">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="a4a41-118">Jeśli aplikacja jest samodzielna, pliki zestawu, które zawierają środowisko uruchomieniowe platformy .NET, znajdują się w folderze *Publikowanie* .</span><span class="sxs-lookup"><span data-stu-id="a4a41-118">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="a4a41-119">Jeśli aplikacja jest zależna od struktury, pliki środowiska uruchomieniowego platformy .NET nie są uwzględniane, ponieważ aplikacja ma odwołanie do wersji platformy .NET zainstalowanej na serwerze.</span><span class="sxs-lookup"><span data-stu-id="a4a41-119">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="a4a41-120">Domyślny model wdrażania jest zależny od platformy.</span><span class="sxs-lookup"><span data-stu-id="a4a41-120">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="a4a41-121">Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="a4a41-121">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="a4a41-122">Oprócz plików *exe* i *dll* , folder *publikacji* aplikacji ASP.NET Core zwykle zawiera pliki konfiguracji, zasoby statyczne i widoki MVC.</span><span class="sxs-lookup"><span data-stu-id="a4a41-122">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="a4a41-123">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="a4a41-123">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="a4a41-124">Konfigurowanie Menedżera procesów</span><span class="sxs-lookup"><span data-stu-id="a4a41-124">Set up a process manager</span></span>

<span data-ttu-id="a4a41-125">Aplikacja ASP.NET Core jest aplikacją konsolową, która musi zostać uruchomiona w przypadku awarii i ponownego uruchomienia serwera.</span><span class="sxs-lookup"><span data-stu-id="a4a41-125">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="a4a41-126">Aby zautomatyzować uruchamianie i ponowne uruchamianie, wymagany jest Menedżer procesów.</span><span class="sxs-lookup"><span data-stu-id="a4a41-126">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="a4a41-127">Najbardziej typowymi menedżerami procesów dla ASP.NET Core są:</span><span class="sxs-lookup"><span data-stu-id="a4a41-127">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="a4a41-128">Linux</span><span class="sxs-lookup"><span data-stu-id="a4a41-128">Linux</span></span>
  * [<span data-ttu-id="a4a41-129">Nginx</span><span class="sxs-lookup"><span data-stu-id="a4a41-129">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="a4a41-130">Apache</span><span class="sxs-lookup"><span data-stu-id="a4a41-130">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="a4a41-131">Windows</span><span class="sxs-lookup"><span data-stu-id="a4a41-131">Windows</span></span>
  * [<span data-ttu-id="a4a41-132">IIS</span><span class="sxs-lookup"><span data-stu-id="a4a41-132">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="a4a41-133">Usługa systemu Windows</span><span class="sxs-lookup"><span data-stu-id="a4a41-133">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="a4a41-134">Konfigurowanie zwrotnego serwera proxy</span><span class="sxs-lookup"><span data-stu-id="a4a41-134">Set up a reverse proxy</span></span>

<span data-ttu-id="a4a41-135">Jeśli aplikacja używa serwera [Kestrel](xref:fundamentals/servers/kestrel) , usługi [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache)lub [IIS](xref:host-and-deploy/iis/index) mogą być używane jako zwrotny serwer proxy.</span><span class="sxs-lookup"><span data-stu-id="a4a41-135">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="a4a41-136">Odwrotny serwer proxy odbiera żądania HTTP z Internetu i przekazuje je do usługi Kestrel.</span><span class="sxs-lookup"><span data-stu-id="a4a41-136">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="a4a41-137">Jest to &mdash; obsługiwana konfiguracja hostingu, która jest konfiguracją z serwerem odwrotnego serwera proxy lub bez niego &mdash; .</span><span class="sxs-lookup"><span data-stu-id="a4a41-137">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="a4a41-138">Aby uzyskać więcej informacji, zobacz [Kiedy używać Kestrel z zwrotnym serwerem proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="a4a41-138">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a4a41-139">Scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="a4a41-139">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a4a41-140">Dodatkowa konfiguracja może być wymagana dla aplikacji hostowanych w ramach serwerów proxy i modułów równoważenia obciążenia.</span><span class="sxs-lookup"><span data-stu-id="a4a41-140">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="a4a41-141">Bez dodatkowej konfiguracji aplikacja może nie mieć dostępu do schematu (HTTP/HTTPS) i zdalnego adresu IP, z którego pochodzi żądanie.</span><span class="sxs-lookup"><span data-stu-id="a4a41-141">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="a4a41-142">Aby uzyskać więcej informacji, zobacz [konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="a4a41-142">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="a4a41-143">Korzystanie z programu Visual Studio i narzędzia MSBuild do automatyzowania wdrożeń</span><span class="sxs-lookup"><span data-stu-id="a4a41-143">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="a4a41-144">Wdrożenie często wymaga dodatkowych zadań oprócz kopiowania danych wyjściowych z [dotnet Publish](/dotnet/core/tools/dotnet-publish) na serwer.</span><span class="sxs-lookup"><span data-stu-id="a4a41-144">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="a4a41-145">Na przykład dodatkowe pliki mogą być wymagane lub wykluczone z folderu *publikacji* .</span><span class="sxs-lookup"><span data-stu-id="a4a41-145">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="a4a41-146">Program Visual Studio używa programu [MSBuild](/visualstudio/msbuild/msbuild) do wdrażania w sieci Web, a programu MSBuild można dostosować do wykonywania wielu innych zadań podczas wdrażania.</span><span class="sxs-lookup"><span data-stu-id="a4a41-146">Visual Studio uses [MSBuild](/visualstudio/msbuild/msbuild) for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="a4a41-147">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/visual-studio-publish-profiles> i książka [using programu MSBuild i Team Foundation Build](http://msbuildbook.com/) .</span><span class="sxs-lookup"><span data-stu-id="a4a41-147">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="a4a41-148">Za pomocą [funkcji publikowania w sieci Web](xref:tutorials/publish-to-azure-webapp-using-vs) lub [wbudowanej obsługi narzędzia Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment)aplikacje można wdrażać bezpośrednio z programu Visual Studio do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="a4a41-148">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="a4a41-149">Azure DevOps Services obsługuje [ciągłe wdrażanie do Azure App Service](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="a4a41-149">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="a4a41-150">Aby uzyskać więcej informacji, zobacz [DevOps with ASP.NET Core i platformę Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="a4a41-150">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="a4a41-151">Publikowanie na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="a4a41-151">Publish to Azure</span></span>

<span data-ttu-id="a4a41-152">Zobacz, <xref:tutorials/publish-to-azure-webapp-using-vs> Aby uzyskać instrukcje dotyczące publikowania aplikacji na platformie Azure przy użyciu programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a4a41-152">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="a4a41-153">Dodatkowy przykład jest zapewniany przez [utworzenie aplikacji sieci web ASP.NET Core na platformie Azure](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="a4a41-153">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="a4a41-154">Publikowanie z MSDeploy w systemie Windows</span><span class="sxs-lookup"><span data-stu-id="a4a41-154">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="a4a41-155">Zobacz <xref:host-and-deploy/visual-studio-publish-profiles> , aby uzyskać instrukcje dotyczące publikowania aplikacji przy użyciu profilu publikacji programu Visual Studio, w tym z wiersza polecenia systemu Windows przy użyciu polecenia [MSBuild programu dotnet](/dotnet/core/tools/dotnet-msbuild) .</span><span class="sxs-lookup"><span data-stu-id="a4a41-155">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="a4a41-156">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="a4a41-156">Internet Information Services (IIS)</span></span>

<span data-ttu-id="a4a41-157">W przypadku wdrożeń do Internet Information Services (IIS) z konfiguracją dostarczoną przez plik *web.config* zapoznaj się z artykułami poniżej <xref:host-and-deploy/iis/index> .</span><span class="sxs-lookup"><span data-stu-id="a4a41-157">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="a4a41-158">Hosting w farmie internetowej</span><span class="sxs-lookup"><span data-stu-id="a4a41-158">Host in a web farm</span></span>

<span data-ttu-id="a4a41-159">Aby uzyskać informacje na temat konfiguracji do hostowania aplikacji ASP.NET Core w środowisku kolektywu serwerów sieci Web (na przykład wdrażania wielu wystąpień aplikacji na potrzeby skalowalności), zobacz <xref:host-and-deploy/web-farm> .</span><span class="sxs-lookup"><span data-stu-id="a4a41-159">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="a4a41-160">Host na platformie Docker</span><span class="sxs-lookup"><span data-stu-id="a4a41-160">Host on Docker</span></span>

<span data-ttu-id="a4a41-161">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="a4a41-161">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="perform-health-checks"></a><span data-ttu-id="a4a41-162">Przeprowadzanie kontroli kondycji</span><span class="sxs-lookup"><span data-stu-id="a4a41-162">Perform health checks</span></span>

<span data-ttu-id="a4a41-163">Używaj oprogramowania do sprawdzania kondycji, aby przeprowadzać kontrole kondycji aplikacji i jej zależności.</span><span class="sxs-lookup"><span data-stu-id="a4a41-163">Use Health Check Middleware to perform health checks on an app and its dependencies.</span></span> <span data-ttu-id="a4a41-164">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/health-checks>.</span><span class="sxs-lookup"><span data-stu-id="a4a41-164">For more information, see <xref:host-and-deploy/health-checks>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a4a41-165">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="a4a41-165">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="a4a41-166">Hosting ASP.NET</span><span class="sxs-lookup"><span data-stu-id="a4a41-166">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="a4a41-167">Ogólnie rzecz biorąc, aby wdrożyć aplikację ASP.NET Core w środowisku hostingu:</span><span class="sxs-lookup"><span data-stu-id="a4a41-167">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="a4a41-168">Wdróż opublikowaną aplikację w folderze na serwerze hostingu.</span><span class="sxs-lookup"><span data-stu-id="a4a41-168">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="a4a41-169">Skonfiguruj Menedżera procesów uruchamiający aplikację po nadejściu żądań i ponownym uruchomieniu aplikacji po awarii lub ponownym uruchomieniu serwera.</span><span class="sxs-lookup"><span data-stu-id="a4a41-169">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="a4a41-170">W przypadku konfiguracji zwrotnego serwera proxy Skonfiguruj zwrotny serwer proxy do przesyłania dalej żądań do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a4a41-170">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="a4a41-171">Publikowanie w folderze</span><span class="sxs-lookup"><span data-stu-id="a4a41-171">Publish to a folder</span></span>

<span data-ttu-id="a4a41-172">[Dotnet Publish](/dotnet/core/tools/dotnet-publish) polecenie kompiluje kod aplikacji i kopiuje pliki wymagane do uruchomienia aplikacji w folderze *publikacji* .</span><span class="sxs-lookup"><span data-stu-id="a4a41-172">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="a4a41-173">Podczas wdrażania z programu Visual Studio, ten `dotnet publish` krok odbywa się automatycznie przed skopiowaniem plików do miejsca docelowego wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="a4a41-173">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="a4a41-174">Zawartość folderu</span><span class="sxs-lookup"><span data-stu-id="a4a41-174">Folder contents</span></span>

<span data-ttu-id="a4a41-175">Folder *publikowania* zawiera jeden lub więcej plików zestawów aplikacji, zależności i opcjonalnie środowiska uruchomieniowego platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="a4a41-175">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="a4a41-176">Aplikację platformy .NET Core można opublikować jako *samodzielne wdrożenie* lub *wdrożenie zależne od platformy*.</span><span class="sxs-lookup"><span data-stu-id="a4a41-176">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="a4a41-177">Jeśli aplikacja jest samodzielna, pliki zestawu, które zawierają środowisko uruchomieniowe platformy .NET, znajdują się w folderze *Publikowanie* .</span><span class="sxs-lookup"><span data-stu-id="a4a41-177">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="a4a41-178">Jeśli aplikacja jest zależna od struktury, pliki środowiska uruchomieniowego platformy .NET nie są uwzględniane, ponieważ aplikacja ma odwołanie do wersji platformy .NET zainstalowanej na serwerze.</span><span class="sxs-lookup"><span data-stu-id="a4a41-178">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="a4a41-179">Domyślny model wdrażania jest zależny od platformy.</span><span class="sxs-lookup"><span data-stu-id="a4a41-179">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="a4a41-180">Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="a4a41-180">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="a4a41-181">Oprócz plików *exe* i *dll* , folder *publikacji* aplikacji ASP.NET Core zwykle zawiera pliki konfiguracji, zasoby statyczne i widoki MVC.</span><span class="sxs-lookup"><span data-stu-id="a4a41-181">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="a4a41-182">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="a4a41-182">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="a4a41-183">Konfigurowanie Menedżera procesów</span><span class="sxs-lookup"><span data-stu-id="a4a41-183">Set up a process manager</span></span>

<span data-ttu-id="a4a41-184">Aplikacja ASP.NET Core jest aplikacją konsolową, która musi zostać uruchomiona w przypadku awarii i ponownego uruchomienia serwera.</span><span class="sxs-lookup"><span data-stu-id="a4a41-184">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="a4a41-185">Aby zautomatyzować uruchamianie i ponowne uruchamianie, wymagany jest Menedżer procesów.</span><span class="sxs-lookup"><span data-stu-id="a4a41-185">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="a4a41-186">Najbardziej typowymi menedżerami procesów dla ASP.NET Core są:</span><span class="sxs-lookup"><span data-stu-id="a4a41-186">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="a4a41-187">Linux</span><span class="sxs-lookup"><span data-stu-id="a4a41-187">Linux</span></span>
  * [<span data-ttu-id="a4a41-188">Nginx</span><span class="sxs-lookup"><span data-stu-id="a4a41-188">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="a4a41-189">Apache</span><span class="sxs-lookup"><span data-stu-id="a4a41-189">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="a4a41-190">Windows</span><span class="sxs-lookup"><span data-stu-id="a4a41-190">Windows</span></span>
  * [<span data-ttu-id="a4a41-191">IIS</span><span class="sxs-lookup"><span data-stu-id="a4a41-191">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="a4a41-192">Usługa systemu Windows</span><span class="sxs-lookup"><span data-stu-id="a4a41-192">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="a4a41-193">Konfigurowanie zwrotnego serwera proxy</span><span class="sxs-lookup"><span data-stu-id="a4a41-193">Set up a reverse proxy</span></span>

<span data-ttu-id="a4a41-194">Jeśli aplikacja używa serwera [Kestrel](xref:fundamentals/servers/kestrel) , usługi [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache)lub [IIS](xref:host-and-deploy/iis/index) mogą być używane jako zwrotny serwer proxy.</span><span class="sxs-lookup"><span data-stu-id="a4a41-194">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="a4a41-195">Odwrotny serwer proxy odbiera żądania HTTP z Internetu i przekazuje je do usługi Kestrel.</span><span class="sxs-lookup"><span data-stu-id="a4a41-195">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="a4a41-196">Jest to &mdash; obsługiwana konfiguracja hostingu, która jest konfiguracją z serwerem odwrotnego serwera proxy lub bez niego &mdash; .</span><span class="sxs-lookup"><span data-stu-id="a4a41-196">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="a4a41-197">Aby uzyskać więcej informacji, zobacz [Kiedy używać Kestrel z zwrotnym serwerem proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="a4a41-197">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a4a41-198">Scenariusze serwera proxy i modułu równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="a4a41-198">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a4a41-199">Dodatkowa konfiguracja może być wymagana dla aplikacji hostowanych w ramach serwerów proxy i modułów równoważenia obciążenia.</span><span class="sxs-lookup"><span data-stu-id="a4a41-199">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="a4a41-200">Bez dodatkowej konfiguracji aplikacja może nie mieć dostępu do schematu (HTTP/HTTPS) i zdalnego adresu IP, z którego pochodzi żądanie.</span><span class="sxs-lookup"><span data-stu-id="a4a41-200">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="a4a41-201">Aby uzyskać więcej informacji, zobacz [konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="a4a41-201">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="a4a41-202">Korzystanie z programu Visual Studio i narzędzia MSBuild do automatyzowania wdrożeń</span><span class="sxs-lookup"><span data-stu-id="a4a41-202">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="a4a41-203">Wdrożenie często wymaga dodatkowych zadań oprócz kopiowania danych wyjściowych z [dotnet Publish](/dotnet/core/tools/dotnet-publish) na serwer.</span><span class="sxs-lookup"><span data-stu-id="a4a41-203">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="a4a41-204">Na przykład dodatkowe pliki mogą być wymagane lub wykluczone z folderu *publikacji* .</span><span class="sxs-lookup"><span data-stu-id="a4a41-204">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="a4a41-205">Program Visual Studio używa programu MSBuild do wdrażania w sieci Web, a programu MSBuild można dostosować do wykonywania wielu innych zadań podczas wdrażania.</span><span class="sxs-lookup"><span data-stu-id="a4a41-205">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="a4a41-206">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/visual-studio-publish-profiles> i książka [using programu MSBuild i Team Foundation Build](http://msbuildbook.com/) .</span><span class="sxs-lookup"><span data-stu-id="a4a41-206">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="a4a41-207">Za pomocą [funkcji publikowania w sieci Web](xref:tutorials/publish-to-azure-webapp-using-vs) lub [wbudowanej obsługi narzędzia Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment)aplikacje można wdrażać bezpośrednio z programu Visual Studio do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="a4a41-207">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="a4a41-208">Azure DevOps Services obsługuje [ciągłe wdrażanie do Azure App Service](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="a4a41-208">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="a4a41-209">Aby uzyskać więcej informacji, zobacz [DevOps with ASP.NET Core i platformę Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="a4a41-209">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="a4a41-210">Publikowanie na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="a4a41-210">Publish to Azure</span></span>

<span data-ttu-id="a4a41-211">Zobacz, <xref:tutorials/publish-to-azure-webapp-using-vs> Aby uzyskać instrukcje dotyczące publikowania aplikacji na platformie Azure przy użyciu programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a4a41-211">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="a4a41-212">Dodatkowy przykład jest zapewniany przez [utworzenie aplikacji sieci web ASP.NET Core na platformie Azure](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="a4a41-212">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="a4a41-213">Publikowanie z MSDeploy w systemie Windows</span><span class="sxs-lookup"><span data-stu-id="a4a41-213">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="a4a41-214">Zobacz <xref:host-and-deploy/visual-studio-publish-profiles> , aby uzyskać instrukcje dotyczące publikowania aplikacji przy użyciu profilu publikacji programu Visual Studio, w tym z wiersza polecenia systemu Windows przy użyciu polecenia [MSBuild programu dotnet](/dotnet/core/tools/dotnet-msbuild) .</span><span class="sxs-lookup"><span data-stu-id="a4a41-214">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="a4a41-215">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="a4a41-215">Internet Information Services (IIS)</span></span>

<span data-ttu-id="a4a41-216">W przypadku wdrożeń do Internet Information Services (IIS) z konfiguracją dostarczoną przez plik *web.config* zapoznaj się z artykułami poniżej <xref:host-and-deploy/iis/index> .</span><span class="sxs-lookup"><span data-stu-id="a4a41-216">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="a4a41-217">Hosting w farmie internetowej</span><span class="sxs-lookup"><span data-stu-id="a4a41-217">Host in a web farm</span></span>

<span data-ttu-id="a4a41-218">Aby uzyskać informacje na temat konfiguracji do hostowania aplikacji ASP.NET Core w środowisku kolektywu serwerów sieci Web (na przykład wdrażania wielu wystąpień aplikacji na potrzeby skalowalności), zobacz <xref:host-and-deploy/web-farm> .</span><span class="sxs-lookup"><span data-stu-id="a4a41-218">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="a4a41-219">Host na platformie Docker</span><span class="sxs-lookup"><span data-stu-id="a4a41-219">Host on Docker</span></span>

<span data-ttu-id="a4a41-220">Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="a4a41-220">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a4a41-221">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="a4a41-221">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="a4a41-222">Hosting ASP.NET</span><span class="sxs-lookup"><span data-stu-id="a4a41-222">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
