---
title: Narzędzia kontenerów programu Visual Studio z platformą ASP.NET Core
author: spboyer
description: Dowiedz się, jak używać narzędzi programu Visual Studio i platformy Docker dla systemu Windows w celu konteneryzowania aplikacji ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: b73903eae2b289fd410599748c1f48bdb4601543
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776132"
---
# <a name="visual-studio-container-tools-with-aspnet-core"></a><span data-ttu-id="1545f-103">Narzędzia kontenerów programu Visual Studio z platformą ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1545f-103">Visual Studio Container Tools with ASP.NET Core</span></span>

<span data-ttu-id="1545f-104">Program Visual Studio 2017 i jego nowsze wersje obsługują kompilowanie, debugowanie i uruchamianie konteneryzowanych aplikacji ASP.NET Core przeznaczonych dla platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1545f-104">Visual Studio 2017 and later versions support building, debugging, and running containerized ASP.NET Core apps targeting .NET Core.</span></span> <span data-ttu-id="1545f-105">Obsługiwane są kontenery zarówno systemu Windows, jak i Linux.</span><span class="sxs-lookup"><span data-stu-id="1545f-105">Both Windows and Linux containers are supported.</span></span>

<span data-ttu-id="1545f-106">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1545f-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1545f-107">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="1545f-107">Prerequisites</span></span>

* [<span data-ttu-id="1545f-108">Docker for Windows</span><span class="sxs-lookup"><span data-stu-id="1545f-108">Docker for Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)
* <span data-ttu-id="1545f-109">[Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z obciążeniem **Programowanie dla wielu platform w środowisku .NET Core**</span><span class="sxs-lookup"><span data-stu-id="1545f-109">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **.NET Core cross-platform development** workload</span></span>

## <a name="installation-and-setup"></a><span data-ttu-id="1545f-110">Instalacja i konfiguracja</span><span class="sxs-lookup"><span data-stu-id="1545f-110">Installation and setup</span></span>

<span data-ttu-id="1545f-111">W przypadku instalacji platformy Docker zapoznaj się z informacjami w [Docker for Windows: co należy wiedzieć przed zainstalowaniem](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)programu.</span><span class="sxs-lookup"><span data-stu-id="1545f-111">For Docker installation, first review the information at [Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).</span></span> <span data-ttu-id="1545f-112">Następnie zainstaluj [platformę Docker dla systemu Windows](https://docs.docker.com/docker-for-windows/install/).</span><span class="sxs-lookup"><span data-stu-id="1545f-112">Next, install [Docker For Windows](https://docs.docker.com/docker-for-windows/install/).</span></span>

<span data-ttu-id="1545f-113">**[Udostępnione dyski](https://docs.docker.com/docker-for-windows/#shared-drives)** na platformie Docker dla systemu Windows muszą być skonfigurowane do obsługi mapowania woluminów i debugowania.</span><span class="sxs-lookup"><span data-stu-id="1545f-113">**[Shared Drives](https://docs.docker.com/docker-for-windows/#shared-drives)** in Docker for Windows must be configured to support volume mapping and debugging.</span></span> <span data-ttu-id="1545f-114">Kliknij prawym przyciskiem myszy ikonę platformy Docker na pasku zadań, wybierz pozycję **Settings** (Ustawienia) i wybierz pozycję **Shared Drives** (Udostępnione dyski).</span><span class="sxs-lookup"><span data-stu-id="1545f-114">Right-click the System Tray's Docker icon, select **Settings**, and select **Shared Drives**.</span></span> <span data-ttu-id="1545f-115">Wybierz dysk, na którym platforma Docker przechowuje pliki.</span><span class="sxs-lookup"><span data-stu-id="1545f-115">Select the drive where Docker stores files.</span></span> <span data-ttu-id="1545f-116">Kliknij przycisk **Zastosuj**.</span><span class="sxs-lookup"><span data-stu-id="1545f-116">Click **Apply**.</span></span>

![Okno dialogowe umożliwiające wybranie lokalnego udostępnionego dysku C na potrzeby kontenerów](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> <span data-ttu-id="1545f-118">W programie Visual Studio 2017 w wersji 15.6 i nowszych jest wyświetlany monit, jeśli **udostępnione dyski** nie są skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="1545f-118">Visual Studio 2017 versions 15.6 and later prompt when **Shared Drives** aren't configured.</span></span>

## <a name="add-a-project-to-a-docker-container"></a><span data-ttu-id="1545f-119">Dodawanie projektu do kontenera platformy Docker</span><span class="sxs-lookup"><span data-stu-id="1545f-119">Add a project to a Docker container</span></span>

<span data-ttu-id="1545f-120">Aby umieścić w kontenerze projekt ASP.NET Core, musi on być przeznaczony dla platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1545f-120">To containerize an ASP.NET Core project, the project must target .NET Core.</span></span> <span data-ttu-id="1545f-121">Obsługiwane są kontenery zarówno systemu Linux, jak i Windows.</span><span class="sxs-lookup"><span data-stu-id="1545f-121">Both Linux and Windows containers are supported.</span></span>

<span data-ttu-id="1545f-122">Podczas dodawania obsługi platformy Docker do projektu wybierz kontener systemu Windows lub Linux.</span><span class="sxs-lookup"><span data-stu-id="1545f-122">When adding Docker support to a project, choose either a Windows or a Linux container.</span></span> <span data-ttu-id="1545f-123">Na hoście platformy Docker musi być uruchomiony ten sam typ kontenera.</span><span class="sxs-lookup"><span data-stu-id="1545f-123">The Docker host must be running the same container type.</span></span> <span data-ttu-id="1545f-124">Aby zmienić typ kontenera w uruchomionym wystąpieniu platformy Docker, kliknij prawym przyciskiem myszy ikonę platformy Docker na pasku zadań i wybierz polecenie **Switch to Windows containers...** (Przełącz do kontenerów systemu Windows...) lub polecenie **Switch to Linux containers...** (Przełącz do kontenerów systemu Linux...).</span><span class="sxs-lookup"><span data-stu-id="1545f-124">To change the container type in the running Docker instance, right-click the System Tray's Docker icon and choose **Switch to Windows containers...** or **Switch to Linux containers...**.</span></span>

### <a name="new-app"></a><span data-ttu-id="1545f-125">Nowa aplikacja</span><span class="sxs-lookup"><span data-stu-id="1545f-125">New app</span></span>

<span data-ttu-id="1545f-126">Podczas tworzenia nowej aplikacji przy użyciu szablonów projektu **Aplikacja internetowa platformy ASP.NET Core** zaznacz pole wyboru **Włącz obsługę platformy Docker**:</span><span class="sxs-lookup"><span data-stu-id="1545f-126">When creating a new app with the **ASP.NET Core Web Application** project templates, select the **Enable Docker Support** check box:</span></span>

![Pole wyboru Włącz obsługę platformy Docker](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

<span data-ttu-id="1545f-128">Jeśli platformą docelową jest .NET Core, lista rozwijana **System operacyjny** umożliwia wybranie typu kontenera.</span><span class="sxs-lookup"><span data-stu-id="1545f-128">If the target framework is .NET Core, the **OS** drop-down allows for the selection of a container type.</span></span>

### <a name="existing-app"></a><span data-ttu-id="1545f-129">Istniejąca aplikacja</span><span class="sxs-lookup"><span data-stu-id="1545f-129">Existing app</span></span>

<span data-ttu-id="1545f-130">W przypadku projektów ASP.NET Core przeznaczonych dla platformy .NET Core dostępne są dwie opcje dodawania obsługi platformy Docker za pomocą narzędzi.</span><span class="sxs-lookup"><span data-stu-id="1545f-130">For ASP.NET Core projects targeting .NET Core, there are two options for adding Docker support via the tooling.</span></span> <span data-ttu-id="1545f-131">Otwórz projekt w programie Visual Studio, a następnie wybierz jedną z następujących opcji:</span><span class="sxs-lookup"><span data-stu-id="1545f-131">Open the project in Visual Studio, and choose one of the following options:</span></span>

* <span data-ttu-id="1545f-132">Wybierz pozycję **Obsługa platformy Docker** z menu **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="1545f-132">Select **Docker Support** from the **Project** menu.</span></span>
* <span data-ttu-id="1545f-133">Kliknij projekt prawym przyciskiem myszy w **Eksploratorze rozwiązań** i wybierz pozycję **Dodaj** > **Obsługa platformy Docker**.</span><span class="sxs-lookup"><span data-stu-id="1545f-133">Right-click the project in **Solution Explorer** and select **Add** > **Docker Support**.</span></span>

<span data-ttu-id="1545f-134">Narzędzia kontenerów programu Visual Studio nie obsługują dodawania platformy Docker do istniejącego projektu ASP.NET Core przeznaczonego dla platformy .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="1545f-134">The Visual Studio Container Tools don't support adding Docker to an existing ASP.NET Core project targeting .NET Framework.</span></span>

## <a name="dockerfile-overview"></a><span data-ttu-id="1545f-135">Plik Dockerfile — przegląd</span><span class="sxs-lookup"><span data-stu-id="1545f-135">Dockerfile overview</span></span>

<span data-ttu-id="1545f-136">Plik *Dockerfile*, zawierający opis tworzenia końcowego obrazu platformy Docker, jest dodawany do katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="1545f-136">A *Dockerfile*, the recipe for creating a final Docker image, is added to the project root.</span></span> <span data-ttu-id="1545f-137">Opis poleceń znajdujących się w tym pliku można znaleźć w [dokumentacji pliku Dockerfile](https://docs.docker.com/engine/reference/builder/).</span><span class="sxs-lookup"><span data-stu-id="1545f-137">Refer to [Dockerfile reference](https://docs.docker.com/engine/reference/builder/) for an understanding of the commands within it.</span></span> <span data-ttu-id="1545f-138">Ten konkretny plik *Dockerfile* używa [kompilacji wieloetapowej](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) z czterema odrębnymi, nazwanymi etapami kompilacji:</span><span class="sxs-lookup"><span data-stu-id="1545f-138">This particular *Dockerfile* uses a [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) with four distinct, named build stages:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

<span data-ttu-id="1545f-139">Wcześniejszy plik *Dockerfile* bazuje na obrazie [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/).</span><span class="sxs-lookup"><span data-stu-id="1545f-139">The preceding *Dockerfile* is based on the [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/) image.</span></span> <span data-ttu-id="1545f-140">Ten obraz podstawowy obejmuje środowisko uruchomieniowe ASP.NET Core i pakiety NuGet.</span><span class="sxs-lookup"><span data-stu-id="1545f-140">This base image includes the ASP.NET Core runtime and NuGet packages.</span></span> <span data-ttu-id="1545f-141">Pakiety są kompilowane w trybie Just-In-Time (JIT), aby zwiększyć wydajność uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="1545f-141">The packages are just-in-time (JIT) compiled to improve startup performance.</span></span>

<span data-ttu-id="1545f-142">Jeśli w oknie dialogowym nowego projektu zostanie zaznaczone pole wyboru **Konfiguruj dla protokołu HTTPS**, plik *Dockerfile* uwidacznia dwa porty.</span><span class="sxs-lookup"><span data-stu-id="1545f-142">When the new project dialog's **Configure for HTTPS** check box is checked, the *Dockerfile* exposes two ports.</span></span> <span data-ttu-id="1545f-143">Jeden port jest używany na potrzeby ruchu HTTP, a drugi na potrzeby protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1545f-143">One port is used for HTTP traffic; the other port is used for HTTPS.</span></span> <span data-ttu-id="1545f-144">Jeśli to pole wyboru nie zostanie zaznaczone, dla ruchu HTTP zostanie uwidoczniony pojedynczy port (80).</span><span class="sxs-lookup"><span data-stu-id="1545f-144">If the check box isn't checked, a single port (80) is exposed for HTTP traffic.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

<span data-ttu-id="1545f-145">Wcześniejszy plik *Dockerfile* bazuje na obrazie [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/).</span><span class="sxs-lookup"><span data-stu-id="1545f-145">The preceding *Dockerfile* is based on the [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) image.</span></span> <span data-ttu-id="1545f-146">Ten obraz podstawowy zawiera pakiety NuGet ASP.NET Core, które są kompilowane w trybie Just-In-Time (JIT), aby zwiększyć wydajność uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="1545f-146">This base image includes the ASP.NET Core NuGet packages, which are just-in-time (JIT) compiled to improve startup performance.</span></span>

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a><span data-ttu-id="1545f-147">Dodawanie do aplikacji obsługi orkiestratora kontenerów</span><span class="sxs-lookup"><span data-stu-id="1545f-147">Add container orchestrator support to an app</span></span>

<span data-ttu-id="1545f-148">Program Visual Studio 2017 w wersji 15.7 lub starszych jako rozwiązanie aranżacji kontenerów obsługuje tylko narzędzie [Docker Compose](https://docs.docker.com/compose/overview/).</span><span class="sxs-lookup"><span data-stu-id="1545f-148">Visual Studio 2017 versions 15.7 or earlier support [Docker Compose](https://docs.docker.com/compose/overview/) as the sole container orchestration solution.</span></span> <span data-ttu-id="1545f-149">Docker Compose artefakty są dodawane za pośrednictwem **dodawania** > **obsługi platformy Docker**.</span><span class="sxs-lookup"><span data-stu-id="1545f-149">The Docker Compose artifacts are added via **Add** > **Docker Support**.</span></span>

<span data-ttu-id="1545f-150">W programie Visual Studio 2017 w wersji 15.8 i nowszych rozwiązanie aranżacji jest dodawane tylko na wyraźne żądanie.</span><span class="sxs-lookup"><span data-stu-id="1545f-150">Visual Studio 2017 versions 15.8 or later add an orchestration solution only when instructed.</span></span> <span data-ttu-id="1545f-151">Kliknij projekt prawym przyciskiem myszy w **Eksploratorze rozwiązań** i wybierz pozycję **Dodaj** > **Obsługa orkiestratora kontenerów**.</span><span class="sxs-lookup"><span data-stu-id="1545f-151">Right-click the project in **Solution Explorer** and select **Add** > **Container Orchestrator Support**.</span></span> <span data-ttu-id="1545f-152">Oferowane są dwa różne opcje: [Docker Compose](#docker-compose) i [Service Fabric](#service-fabric).</span><span class="sxs-lookup"><span data-stu-id="1545f-152">Two different choices are offered: [Docker Compose](#docker-compose) and [Service Fabric](#service-fabric).</span></span>

### <a name="docker-compose"></a><span data-ttu-id="1545f-153">Docker Compose</span><span class="sxs-lookup"><span data-stu-id="1545f-153">Docker Compose</span></span>

<span data-ttu-id="1545f-154">Narzędzia kontenerów programu Visual Studio dodają do rozwiązania projekt *docker-compose* z następującymi plikami:</span><span class="sxs-lookup"><span data-stu-id="1545f-154">The Visual Studio Container Tools add a *docker-compose* project to the solution with the following files:</span></span>

* <span data-ttu-id="1545f-155">*docker-compose.dcproj* &ndash; plik reprezentujący projekt.</span><span class="sxs-lookup"><span data-stu-id="1545f-155">*docker-compose.dcproj* &ndash; The file representing the project.</span></span> <span data-ttu-id="1545f-156">Zawiera element `<DockerTargetOS>` określający system operacyjny, który ma być używany.</span><span class="sxs-lookup"><span data-stu-id="1545f-156">Includes a `<DockerTargetOS>` element specifying the OS to be used.</span></span>
* <span data-ttu-id="1545f-157">*.dockerignore* &ndash; zawiera listę wzorców plików i katalogów, które mają zostać wykluczone podczas generowania kontekstu kompilacji.</span><span class="sxs-lookup"><span data-stu-id="1545f-157">*.dockerignore* &ndash; Lists the file and directory patterns to exclude when generating a build context.</span></span>
* <span data-ttu-id="1545f-158">*docker-compose.yml* &ndash; podstawowy plik [Docker Compose](https://docs.docker.com/compose/overview/) używany do definiowania kolekcji obrazów skompilowanych i uruchomionych za pomocą poleceń `docker-compose build` i `docker-compose run`.</span><span class="sxs-lookup"><span data-stu-id="1545f-158">*docker-compose.yml* &ndash; The base [Docker Compose](https://docs.docker.com/compose/overview/) file used to define the collection of images built and run with `docker-compose build` and `docker-compose run`, respectively.</span></span>
* <span data-ttu-id="1545f-159">*docker-compose.override.yml* &ndash; opcjonalny plik (odczytywany przez narzędzie Docker Compose) zawierający przesłonięcia konfiguracji dla usług.</span><span class="sxs-lookup"><span data-stu-id="1545f-159">*docker-compose.override.yml* &ndash; An optional file, read by Docker Compose, with configuration overrides for services.</span></span> <span data-ttu-id="1545f-160">Program Visual Studio wykonuje polecenie `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"`, aby scalić te pliki.</span><span class="sxs-lookup"><span data-stu-id="1545f-160">Visual Studio executes `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` to merge these files.</span></span>

<span data-ttu-id="1545f-161">Plik *docker-compose.yml* przywołuje nazwę obrazu utworzonego podczas uruchamiania projektu:</span><span class="sxs-lookup"><span data-stu-id="1545f-161">The *docker-compose.yml* file references the name of the image that's created when the project runs:</span></span>

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

<span data-ttu-id="1545f-162">W poprzednim przykładzie polecenie `image: hellodockertools` generuje obraz `hellodockertools:dev`, gdy aplikacja działa w trybie **debugowania**.</span><span class="sxs-lookup"><span data-stu-id="1545f-162">In the preceding example, `image: hellodockertools` generates the image `hellodockertools:dev` when the app runs in **Debug** mode.</span></span> <span data-ttu-id="1545f-163">Obraz `hellodockertools:latest` jest generowany, gdy aplikacja działa w trybie **wydania**.</span><span class="sxs-lookup"><span data-stu-id="1545f-163">The `hellodockertools:latest` image is generated when the app runs in **Release** mode.</span></span>

<span data-ttu-id="1545f-164">Dodaj do nazwy obrazu prefiks [Docker Hub](https://hub.docker.com/) nazwa użytkownika (na przykład `dockerhubusername/hellodockertools`), jeśli obraz jest wypychany do rejestru.</span><span class="sxs-lookup"><span data-stu-id="1545f-164">Prefix the image name with the [Docker Hub](https://hub.docker.com/) username (for example, `dockerhubusername/hellodockertools`) if the image is pushed to the registry.</span></span> <span data-ttu-id="1545f-165">Możesz również zmienić nazwę obrazu tak, aby zawierała adres URL rejestru prywatnego (na przykład `privateregistry.domain.com/hellodockertools`) w zależności od konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="1545f-165">Alternatively, change the image name to include the private registry URL (for example, `privateregistry.domain.com/hellodockertools`) depending on the configuration.</span></span>

<span data-ttu-id="1545f-166">Jeśli chcesz wymusić różne zachowania w zależności od konfiguracji kompilacji (na przykład debugowanie lub wydanie), dodaj specyficzne dla konfiguracji pliki *docker-compose*.</span><span class="sxs-lookup"><span data-stu-id="1545f-166">If you want different behavior based on the build configuration (for example, Debug or Release), add configuration-specific *docker-compose* files.</span></span> <span data-ttu-id="1545f-167">Pliki powinny mieć nazwy zgodne z konfiguracją kompilacji (na przykład *docker-compose.vs.debug.yml* i *docker-compose.vs.release.yml*) i powinny być umieszczone w tej samej lokalizacji co plik *docker-compose-override.yml*.</span><span class="sxs-lookup"><span data-stu-id="1545f-167">The files should be named according to the build configuration (for example, *docker-compose.vs.debug.yml* and *docker-compose.vs.release.yml*) and placed in the same location as the *docker-compose-override.yml* file.</span></span> 

<span data-ttu-id="1545f-168">Przy użyciu plików przesłonięć specyficznych dla konfiguracji można określić różne ustawienia konfiguracji (na przykład zmienne środowiskowe lub punkty wejścia) dla kompilacji w trybie debugowania i wydania.</span><span class="sxs-lookup"><span data-stu-id="1545f-168">Using the configuration-specific override files, you can specify different configuration settings (such as environment variables or entry points) for Debug and Release build configurations.</span></span>

<span data-ttu-id="1545f-169">Aby w narzędziu Docker Compose była wyświetlana opcja uruchamiania w programie Visual Studio, projekt platformy Docker musi być projektem startowym.</span><span class="sxs-lookup"><span data-stu-id="1545f-169">For Docker Compose to display an option to run in Visual Studio, the docker project must be the startup project.</span></span>

### <a name="service-fabric"></a><span data-ttu-id="1545f-170">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="1545f-170">Service Fabric</span></span>

<span data-ttu-id="1545f-171">Oprócz podstawowych [wymagań wstępnych](#prerequisites) rozwiązanie aranżacji [Service Fabric](/azure/service-fabric/) ma następujące wymagania wstępne:</span><span class="sxs-lookup"><span data-stu-id="1545f-171">In addition to the base [Prerequisites](#prerequisites), the [Service Fabric](/azure/service-fabric/) orchestration solution demands the following prerequisites:</span></span>

* <span data-ttu-id="1545f-172">Pakiet [Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) w wersji 2.6 lub nowszej</span><span class="sxs-lookup"><span data-stu-id="1545f-172">[Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) version 2.6 or later</span></span>
* <span data-ttu-id="1545f-173">Obciążenie **programowania platformy Azure** dla programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1545f-173">Visual Studio's **Azure Development** workload</span></span>

<span data-ttu-id="1545f-174">Usługa Service Fabric nie obsługuje uruchamiania kontenerów systemu Linux w lokalnym klastrze programistycznym w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="1545f-174">Service Fabric doesn't support running Linux containers in the local development cluster on Windows.</span></span> <span data-ttu-id="1545f-175">Jeśli projekt używa już kontenera systemu Linux, program Visual Studio wyświetli komunikat z prośbą o przełączenie do kontenerów systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="1545f-175">If the project is already using a Linux container, Visual Studio prompts to switch to Windows containers.</span></span>

<span data-ttu-id="1545f-176">Narzędzia kontenerów programu Visual Studio wykonują następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="1545f-176">The Visual Studio Container Tools do the following tasks:</span></span>

* <span data-ttu-id="1545f-177">Dodaje \* &lt;Project_Name&gt;aplikacji\* **Service Fabric projektu aplikacji** do rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="1545f-177">Adds a *&lt;project_name&gt;Application* **Service Fabric Application** project to the solution.</span></span>
* <span data-ttu-id="1545f-178">Dodają do projektu ASP.NET Core pliki *Dockerfile* i *.dockerignore*.</span><span class="sxs-lookup"><span data-stu-id="1545f-178">Adds a *Dockerfile* and a *.dockerignore* file to the ASP.NET Core project.</span></span> <span data-ttu-id="1545f-179">Jeśli plik *Dockerfile* już istnieje w projekcie ASP.NET Core, jego nazwa zostanie zmieniona na *Dockerfile.original*.</span><span class="sxs-lookup"><span data-stu-id="1545f-179">If a *Dockerfile* already exists in the ASP.NET Core project, it's renamed to *Dockerfile.original*.</span></span> <span data-ttu-id="1545f-180">Tworzony jest nowy plik *Dockerfile* podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="1545f-180">A new *Dockerfile*, similar to the following, is created:</span></span>

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* <span data-ttu-id="1545f-181">Dodają element `<IsServiceFabricServiceProject>` do pliku *.csproj* projektu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="1545f-181">Adds an `<IsServiceFabricServiceProject>` element to the ASP.NET Core project's *.csproj* file:</span></span>

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* <span data-ttu-id="1545f-182">Dodają folder *PackageRoot* do projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1545f-182">Adds a *PackageRoot* folder to the ASP.NET Core project.</span></span> <span data-ttu-id="1545f-183">Ten folder zawiera manifest usługi i ustawienia nowej usługi.</span><span class="sxs-lookup"><span data-stu-id="1545f-183">The folder includes the service manifest and settings for the new service.</span></span>

<span data-ttu-id="1545f-184">Aby uzyskać więcej informacji, zobacz [Wdrażanie aplikacji .NET w kontenerze systemu Windows w usłudze Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).</span><span class="sxs-lookup"><span data-stu-id="1545f-184">For more information, see [Deploy a .NET app in a Windows container to Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).</span></span>

## <a name="debug"></a><span data-ttu-id="1545f-185">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="1545f-185">Debug</span></span>

<span data-ttu-id="1545f-186">Wybierz pozycję **Docker** z listy rozwijanej debugowania na pasku narzędzi i rozpocznij debugowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1545f-186">Select **Docker** from the debug drop-down in the toolbar, and start debugging the app.</span></span> <span data-ttu-id="1545f-187">W widoku **Docker** w oknie **Dane wyjściowe** widać wykonywanie następujących akcji:</span><span class="sxs-lookup"><span data-stu-id="1545f-187">The **Docker** view of the **Output** window shows the following actions taking place:</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="1545f-188">Uzyskiwany jest tag *2.1-aspnetcore-runtime* obrazu środowiska uruchomieniowego *microsoft/dotnet* (jeśli nie znajduje się już w pamięci podręcznej).</span><span class="sxs-lookup"><span data-stu-id="1545f-188">The *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* runtime image is acquired (if not already in the cache).</span></span> <span data-ttu-id="1545f-189">Obraz instaluje środowiska uruchomieniowe platform ASP.NET Core i .NET Core oraz powiązane biblioteki.</span><span class="sxs-lookup"><span data-stu-id="1545f-189">The image installs the ASP.NET Core and .NET Core runtimes and associated libraries.</span></span> <span data-ttu-id="1545f-190">Są one zoptymalizowane pod kątem uruchamiania aplikacji ASP.NET Core w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="1545f-190">It's optimized for running ASP.NET Core apps in production.</span></span>
* <span data-ttu-id="1545f-191">Zmienna środowiskowa `ASPNETCORE_ENVIRONMENT` jest ustawiona na wartość `Development` w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="1545f-191">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="1545f-192">Uwidaczniane są dwa dynamicznie przypisywane porty: jeden dla protokołu HTTP i jeden dla protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1545f-192">Two dynamically assigned ports are exposed: one for HTTP and one for HTTPS.</span></span> <span data-ttu-id="1545f-193">Do portu przypisanego do hosta lokalnego można wysłać polecenie `docker ps`.</span><span class="sxs-lookup"><span data-stu-id="1545f-193">The port assigned to localhost can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="1545f-194">Aplikacja jest kopiowana do kontenera.</span><span class="sxs-lookup"><span data-stu-id="1545f-194">The app is copied to the container.</span></span>
* <span data-ttu-id="1545f-195">Uruchamiana jest domyślna przeglądarka z debugerem dołączonym do kontenera przy użyciu dynamicznie przypisanego portu.</span><span class="sxs-lookup"><span data-stu-id="1545f-195">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="1545f-196">Wynikowy obraz platformy Docker aplikacji jest oznaczony tagiem *dev*.</span><span class="sxs-lookup"><span data-stu-id="1545f-196">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="1545f-197">Obraz bazuje na tagu *2.1-aspnetcore-runtime* obrazu podstawowego *microsoft/dotnet*.</span><span class="sxs-lookup"><span data-stu-id="1545f-197">The image is based on the *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* base image.</span></span> <span data-ttu-id="1545f-198">Uruchom polecenie `docker images` w oknie **konsoli menedżera pakietów** (PMC).</span><span class="sxs-lookup"><span data-stu-id="1545f-198">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="1545f-199">Na komputerze są wyświetlane następujące obrazy:</span><span class="sxs-lookup"><span data-stu-id="1545f-199">The images on the machine are displayed:</span></span>

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* <span data-ttu-id="1545f-200">Uzyskiwany jest obraz środowiska uruchomieniowego *microsoft/aspnetcore* (jeśli nie znajduje się już w pamięci podręcznej).</span><span class="sxs-lookup"><span data-stu-id="1545f-200">The *microsoft/aspnetcore* runtime image is acquired (if not already in the cache).</span></span>
* <span data-ttu-id="1545f-201">Zmienna środowiskowa `ASPNETCORE_ENVIRONMENT` jest ustawiona na wartość `Development` w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="1545f-201">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="1545f-202">Port 80 jest uwidaczniany i mapowany na dynamicznie przypisany port dla hosta lokalnego.</span><span class="sxs-lookup"><span data-stu-id="1545f-202">Port 80 is exposed and mapped to a dynamically assigned port for localhost.</span></span> <span data-ttu-id="1545f-203">Port jest określany przez hosta platformy Docker i można do niego wysłać zapytanie przy użyciu polecenia `docker ps`.</span><span class="sxs-lookup"><span data-stu-id="1545f-203">The port is determined by the Docker host and can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="1545f-204">Aplikacja jest kopiowana do kontenera.</span><span class="sxs-lookup"><span data-stu-id="1545f-204">The app is copied to the container.</span></span>
* <span data-ttu-id="1545f-205">Uruchamiana jest domyślna przeglądarka z debugerem dołączonym do kontenera przy użyciu dynamicznie przypisanego portu.</span><span class="sxs-lookup"><span data-stu-id="1545f-205">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="1545f-206">Wynikowy obraz platformy Docker aplikacji jest oznaczony tagiem *dev*.</span><span class="sxs-lookup"><span data-stu-id="1545f-206">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="1545f-207">Obraz bazuje na obrazie podstawowym *microsoft/aspnetcore*.</span><span class="sxs-lookup"><span data-stu-id="1545f-207">The image is based on the *microsoft/aspnetcore* base image.</span></span> <span data-ttu-id="1545f-208">Uruchom polecenie `docker images` w oknie **konsoli menedżera pakietów** (PMC).</span><span class="sxs-lookup"><span data-stu-id="1545f-208">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="1545f-209">Na komputerze są wyświetlane następujące obrazy:</span><span class="sxs-lookup"><span data-stu-id="1545f-209">The images on the machine are displayed:</span></span>

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="1545f-210">Obraz *dev* nie zawiera zawartości aplikacji, ponieważ konfiguracje **debugowania** używają instalacji woluminu w celu zapewnienia iteracyjnych działań.</span><span class="sxs-lookup"><span data-stu-id="1545f-210">The *dev* image lacks the app contents, as **Debug** configurations use volume mounting to provide the iterative experience.</span></span> <span data-ttu-id="1545f-211">Aby wypchnąć obraz, należy użyć konfiguracji **wydania**.</span><span class="sxs-lookup"><span data-stu-id="1545f-211">To push an image, use the **Release** configuration.</span></span>

<span data-ttu-id="1545f-212">Uruchom polecenie `docker ps` w konsoli PMC.</span><span class="sxs-lookup"><span data-stu-id="1545f-212">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="1545f-213">Zauważ, że aplikacja działa przy użyciu kontenera:</span><span class="sxs-lookup"><span data-stu-id="1545f-213">Notice the app is running using the container:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a><span data-ttu-id="1545f-214">Edytowanie i kontynuowanie</span><span class="sxs-lookup"><span data-stu-id="1545f-214">Edit and continue</span></span>

<span data-ttu-id="1545f-215">Zmiany w plikach i Razor widokach statycznych są automatycznie aktualizowane bez konieczności wykonywania kroku kompilacji.</span><span class="sxs-lookup"><span data-stu-id="1545f-215">Changes to static files and Razor views are automatically updated without the need for a compilation step.</span></span> <span data-ttu-id="1545f-216">Wprowadź zmiany, zapisz je i odśwież przeglądarkę, aby wyświetlić aktualizację.</span><span class="sxs-lookup"><span data-stu-id="1545f-216">Make the change, save, and refresh the browser to view the update.</span></span>

<span data-ttu-id="1545f-217">Modyfikacje pliku kodu wymagają kompilacji i ponownego uruchomienia serwera Kestrel w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="1545f-217">Code file modifications require compilation and a restart of Kestrel within the container.</span></span> <span data-ttu-id="1545f-218">Po wprowadzeniu zmiany użyj klawiszy `CTRL+F5`, aby wykonać przetwarzanie i uruchomić aplikację w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="1545f-218">After making the change, use `CTRL+F5` to perform the process and start the app within the container.</span></span> <span data-ttu-id="1545f-219">Kontener platformy Docker nie jest ponownie kompilowany ani zatrzymywany.</span><span class="sxs-lookup"><span data-stu-id="1545f-219">The Docker container isn't rebuilt or stopped.</span></span> <span data-ttu-id="1545f-220">Uruchom polecenie `docker ps` w konsoli PMC.</span><span class="sxs-lookup"><span data-stu-id="1545f-220">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="1545f-221">Zwróć uwagę, że oryginalny kontener nadal działa od 10 minut:</span><span class="sxs-lookup"><span data-stu-id="1545f-221">Notice the original container is still running as of 10 minutes ago:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a><span data-ttu-id="1545f-222">Publikowanie obrazów platformy Docker</span><span class="sxs-lookup"><span data-stu-id="1545f-222">Publish Docker images</span></span>

<span data-ttu-id="1545f-223">Po zakończeniu cyklu opracowywania i debugowania aplikacji narzędzia kontenerów programu Visual Studio ułatwiają tworzenie obrazu produkcyjnego aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1545f-223">Once the develop and debug cycle of the app is completed, the Visual Studio Container Tools assist in creating the production image of the app.</span></span> <span data-ttu-id="1545f-224">Zmień opcję listy rozwijanej konfiguracji na **Wydanie** i skompiluj aplikację.</span><span class="sxs-lookup"><span data-stu-id="1545f-224">Change the configuration drop-down to **Release** and build the app.</span></span> <span data-ttu-id="1545f-225">Narzędzia uzyskują obraz kompilowania/publikowania z usługi Docker Hub (jeśli jeszcze nie znajduje się on w pamięci podręcznej).</span><span class="sxs-lookup"><span data-stu-id="1545f-225">The tooling acquires the compile/publish image from Docker Hub (if not already in the cache).</span></span> <span data-ttu-id="1545f-226">Obraz jest tworzony z tagiem *latest* (najnowszy) i można go wypchnąć do rejestru prywatnego lub usługi Docker Hub.</span><span class="sxs-lookup"><span data-stu-id="1545f-226">An image is produced with the *latest* tag, which can be pushed to the private registry or Docker Hub.</span></span>

<span data-ttu-id="1545f-227">Uruchom polecenie `docker images` w konsoli PMC, aby wyświetlić listę obrazów.</span><span class="sxs-lookup"><span data-stu-id="1545f-227">Run the `docker images` command in PMC to see the list of images.</span></span> <span data-ttu-id="1545f-228">Wyświetlane są dane wyjściowe podobne do następujących:</span><span class="sxs-lookup"><span data-stu-id="1545f-228">Output similar to the following is displayed:</span></span>

::: moniker range=">= aspnetcore-2.1"

```console
REPOSITORY        TAG                     IMAGE ID      CREATED             SIZE
hellodockertools  latest                  e3984a64230c  About a minute ago  258MB
hellodockertools  dev                     d72ce0f1dfe7  4 minutes ago       255MB
microsoft/dotnet  2.1-sdk                 9e243db15f91  6 days ago          1.7GB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago          255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

```console
REPOSITORY                  TAG     IMAGE ID      CREATED         SIZE
hellodockertools            latest  cd28f0d4abbd  12 seconds ago  349MB
hellodockertools            dev     5fafe5d1ad5b  23 minutes ago  347MB
microsoft/aspnetcore-build  2.0     7fed40fbb647  13 days ago     2.02GB
microsoft/aspnetcore        2.0     c69d39472da9  13 days ago     347MB
```

<span data-ttu-id="1545f-229">Obrazy `microsoft/aspnetcore-build` i `microsoft/aspnetcore` wyświetlane w poprzednich danych wyjściowych zostały zastąpione obrazami `microsoft/dotnet` platformy .NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="1545f-229">The `microsoft/aspnetcore-build` and `microsoft/aspnetcore` images listed in the preceding output are replaced with `microsoft/dotnet` images as of .NET Core 2.1.</span></span> <span data-ttu-id="1545f-230">Aby uzyskać więcej informacji, zobacz [zapowiedź migracji repozytoriów platformy Docker](https://github.com/aspnet/Announcements/issues/298).</span><span class="sxs-lookup"><span data-stu-id="1545f-230">For more information, see [the Docker repositories migration announcement](https://github.com/aspnet/Announcements/issues/298).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="1545f-231">Polecenie zwraca obrazy pośredniczące z nazwami repozytoriów i tagami identyfikowanych jako \* \<brak>\* (niewymienione powyżej). `docker images`</span><span class="sxs-lookup"><span data-stu-id="1545f-231">The `docker images` command returns intermediary images with repository names and tags identified as *\<none>* (not listed above).</span></span> <span data-ttu-id="1545f-232">Te obrazy bez nazwy są tworzone przez [Wieloetapową kompilację](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *pliku dockerfile*.</span><span class="sxs-lookup"><span data-stu-id="1545f-232">These unnamed images are produced by the [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile*.</span></span> <span data-ttu-id="1545f-233">Poprawiają one wydajność kompilowania końcowego obrazu &mdash; w przypadku wystąpienia zmian tylko niezbędne warstwy są ponownie kompilowane.</span><span class="sxs-lookup"><span data-stu-id="1545f-233">They improve the efficiency of building the final image&mdash;only the necessary layers are rebuilt when changes occur.</span></span> <span data-ttu-id="1545f-234">Gdy obrazy pośrednie nie będą już potrzebne, można je usunąć za pomocą polecenia [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/).</span><span class="sxs-lookup"><span data-stu-id="1545f-234">When the intermediary images are no longer needed, delete them using the [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) command.</span></span>

<span data-ttu-id="1545f-235">Może wystąpić oczekiwanie, że rozmiar obrazu produkcyjnego lub wydania będzie mniejszy niż obraz *dev*.</span><span class="sxs-lookup"><span data-stu-id="1545f-235">There may be an expectation for the production or release image to be smaller in size by comparison to the *dev* image.</span></span> <span data-ttu-id="1545f-236">Ze względu na mapowanie woluminu debuger i aplikacja były uruchomione z poziomu komputera lokalnego, a nie w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="1545f-236">Because of the volume mapping, the debugger and app were running from the local machine and not within the container.</span></span> <span data-ttu-id="1545f-237">Obraz *latest* (najnowszy) zawiera spakowany kod aplikacji niezbędny do uruchomienia aplikacji na komputerze hosta.</span><span class="sxs-lookup"><span data-stu-id="1545f-237">The *latest* image has packaged the necessary app code to run the app on a host machine.</span></span> <span data-ttu-id="1545f-238">Różnica w rozmiarach wynika zatem z rozmiaru kodu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1545f-238">Therefore, the delta is the size of the app code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1545f-239">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="1545f-239">Additional resources</span></span>

* [<span data-ttu-id="1545f-240">Opracowywanie kontenerów w programie Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1545f-240">Container development with Visual Studio</span></span>](/visualstudio/containers)
* [<span data-ttu-id="1545f-241">Azure Service Fabric: Przygotowywanie środowiska deweloperskiego</span><span class="sxs-lookup"><span data-stu-id="1545f-241">Azure Service Fabric: Prepare your development environment</span></span>](/azure/service-fabric/service-fabric-get-started)
* [<span data-ttu-id="1545f-242">Wdrażanie aplikacji .NET w kontenerze systemu Windows w usłudze Azure Service Fabric</span><span class="sxs-lookup"><span data-stu-id="1545f-242">Deploy a .NET app in a Windows container to Azure Service Fabric</span></span>](/azure/service-fabric/service-fabric-host-app-in-a-container)
* [<span data-ttu-id="1545f-243">Rozwiązywanie problemów związanych z opracowywaniem zwartości w programie Visual Studio przy użyciu platformy Docker</span><span class="sxs-lookup"><span data-stu-id="1545f-243">Troubleshoot Visual Studio development with Docker</span></span>](/azure/vs-azure-tools-docker-troubleshooting-docker-errors)
* [<span data-ttu-id="1545f-244">Repozytorium usługi GitHub dla narzędzi kontenerów programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1545f-244">Visual Studio Container Tools GitHub repository</span></span>](https://github.com/Microsoft/DockerTools)
* [<span data-ttu-id="1545f-245">GC przy użyciu platformy Docker i małych kontenerów</span><span class="sxs-lookup"><span data-stu-id="1545f-245">GC using Docker and small containers</span></span>](xref:performance/memory#sc)
