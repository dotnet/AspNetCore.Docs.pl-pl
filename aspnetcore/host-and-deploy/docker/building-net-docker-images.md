---
title: Obrazy platformy Docker dla ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać opublikowanych obrazów platformy Docker ASP.NET Core z rejestru platformy Docker. Ściąganie i kompilowanie własnych obrazów.
ms.author: riande
ms.custom: mvc
ms.date: 01/04/2021
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
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 32e721035df8bd9e746ad4db6bb2753c358f3dac
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413499"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="45e1c-104">Obrazy platformy Docker dla ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="45e1c-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="45e1c-105">W tym samouczku pokazano, jak uruchomić aplikację ASP.NET Core w kontenerach platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="45e1c-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="45e1c-106">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="45e1c-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="45e1c-107">Dowiedz się więcej na temat ASP.NET Core obrazów platformy Docker</span><span class="sxs-lookup"><span data-stu-id="45e1c-107">Learn about ASP.NET Core Docker images</span></span>
> * <span data-ttu-id="45e1c-108">Pobieranie przykładowej aplikacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="45e1c-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="45e1c-109">Uruchamianie przykładowej aplikacji lokalnie</span><span class="sxs-lookup"><span data-stu-id="45e1c-109">Run the sample app locally</span></span>
> * <span data-ttu-id="45e1c-110">Uruchamianie przykładowej aplikacji w kontenerach systemu Linux</span><span class="sxs-lookup"><span data-stu-id="45e1c-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="45e1c-111">Uruchamianie przykładowej aplikacji w kontenerach systemu Windows</span><span class="sxs-lookup"><span data-stu-id="45e1c-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="45e1c-112">Kompiluj i wdrażaj ręcznie</span><span class="sxs-lookup"><span data-stu-id="45e1c-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="45e1c-113">ASP.NET Core obrazów platformy Docker</span><span class="sxs-lookup"><span data-stu-id="45e1c-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="45e1c-114">Na potrzeby tego samouczka pobierzesz przykładową aplikację ASP.NET Core i uruchomisz ją w kontenerach platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="45e1c-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="45e1c-115">Przykład działa w przypadku kontenerów systemów Linux i Windows.</span><span class="sxs-lookup"><span data-stu-id="45e1c-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="45e1c-116">Przykładowy pliku dockerfile używa [funkcji budowania wielu etapów platformy Docker](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) do kompilowania i uruchamiania w różnych kontenerach.</span><span class="sxs-lookup"><span data-stu-id="45e1c-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="45e1c-117">Kontenery kompilacji i uruchamiania są tworzone na podstawie obrazów udostępnianych w usłudze Docker Hub przez firmę Microsoft:</span><span class="sxs-lookup"><span data-stu-id="45e1c-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

::: moniker range=">= aspnetcore-5.0"

* `dotnet/sdk`

  <span data-ttu-id="45e1c-118">Przykład używa tego obrazu do kompilowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="45e1c-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="45e1c-119">Obraz zawiera zestaw .NET SDK, który zawiera narzędzia wiersza polecenia (CLI).</span><span class="sxs-lookup"><span data-stu-id="45e1c-119">The image contains the .NET SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="45e1c-120">Obraz jest zoptymalizowany pod kątem lokalnego projektowania, debugowania i testowania jednostkowego.</span><span class="sxs-lookup"><span data-stu-id="45e1c-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="45e1c-121">Narzędzia zainstalowane do programowania i kompilowania sprawiają, że obraz jest stosunkowo duży.</span><span class="sxs-lookup"><span data-stu-id="45e1c-121">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/sdk`

  <span data-ttu-id="45e1c-122">Przykład używa tego obrazu do kompilowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="45e1c-122">The sample uses this image for building the app.</span></span> <span data-ttu-id="45e1c-123">Obraz zawiera zestaw .NET Core SDK, który zawiera narzędzia wiersza polecenia (CLI).</span><span class="sxs-lookup"><span data-stu-id="45e1c-123">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="45e1c-124">Obraz jest zoptymalizowany pod kątem lokalnego projektowania, debugowania i testowania jednostkowego.</span><span class="sxs-lookup"><span data-stu-id="45e1c-124">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="45e1c-125">Narzędzia zainstalowane do programowania i kompilowania sprawiają, że obraz jest stosunkowo duży.</span><span class="sxs-lookup"><span data-stu-id="45e1c-125">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `dotnet/aspnet`

   <span data-ttu-id="45e1c-126">Przykład używa tego obrazu do uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="45e1c-126">The sample uses this image for running the app.</span></span> <span data-ttu-id="45e1c-127">Obraz zawiera ASP.NET Core środowiska uruchomieniowego i bibliotek, które są zoptymalizowane pod kątem uruchamiania aplikacji w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="45e1c-127">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="45e1c-128">Obraz jest stosunkowo mały, zaprojektowany z myślą o szybkości wdrażania i uruchamiania aplikacji, dlatego Optymalizacja wydajności sieci z poziomu rejestru platformy Docker do hosta platformy Docker jest zoptymalizowana.</span><span class="sxs-lookup"><span data-stu-id="45e1c-128">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="45e1c-129">Tylko pliki binarne i zawartość, które są konieczne do uruchomienia aplikacji, są kopiowane do kontenera.</span><span class="sxs-lookup"><span data-stu-id="45e1c-129">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="45e1c-130">Zawartość jest gotowa do uruchomienia, co pozwoli na najszybszy czas od `docker run` do uruchomienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="45e1c-130">The contents are ready to run, enabling the fastest time from `docker run` to app startup.</span></span> <span data-ttu-id="45e1c-131">W modelu platformy Docker nie jest wymagana kompilacja kodu dynamicznego.</span><span class="sxs-lookup"><span data-stu-id="45e1c-131">Dynamic code compilation isn't needed in the Docker model.</span></span>
   
::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/aspnet`

   <span data-ttu-id="45e1c-132">Przykład używa tego obrazu do uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="45e1c-132">The sample uses this image for running the app.</span></span> <span data-ttu-id="45e1c-133">Obraz zawiera ASP.NET Core środowiska uruchomieniowego i bibliotek, które są zoptymalizowane pod kątem uruchamiania aplikacji w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="45e1c-133">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="45e1c-134">Obraz jest stosunkowo mały, zaprojektowany z myślą o szybkości wdrażania i uruchamiania aplikacji, dlatego Optymalizacja wydajności sieci z poziomu rejestru platformy Docker do hosta platformy Docker jest zoptymalizowana.</span><span class="sxs-lookup"><span data-stu-id="45e1c-134">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="45e1c-135">Tylko pliki binarne i zawartość, które są konieczne do uruchomienia aplikacji, są kopiowane do kontenera.</span><span class="sxs-lookup"><span data-stu-id="45e1c-135">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="45e1c-136">Zawartość jest gotowa do uruchomienia, co pozwoli na najszybszy czas od `docker run` do uruchomienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="45e1c-136">The contents are ready to run, enabling the fastest time from `docker run` to app startup.</span></span> <span data-ttu-id="45e1c-137">W modelu platformy Docker nie jest wymagana kompilacja kodu dynamicznego.</span><span class="sxs-lookup"><span data-stu-id="45e1c-137">Dynamic code compilation isn't needed in the Docker model.</span></span>
   
::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="45e1c-138">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="45e1c-138">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

* [<span data-ttu-id="45e1c-139">.NET SDK 5.0</span><span class="sxs-lookup"><span data-stu-id="45e1c-139">.NET SDK 5.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* [<span data-ttu-id="45e1c-140">Zestaw .NET Core SDK 3.1</span><span class="sxs-lookup"><span data-stu-id="45e1c-140">.NET Core SDK 3.1</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="45e1c-141">Zestaw SDK platformy .NET Core 2,2</span><span class="sxs-lookup"><span data-stu-id="45e1c-141">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

::: moniker-end

* <span data-ttu-id="45e1c-142">Klient platformy Docker 18,03 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="45e1c-142">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="45e1c-143">Dystrybucje systemu Linux</span><span class="sxs-lookup"><span data-stu-id="45e1c-143">Linux distributions</span></span>
    * [<span data-ttu-id="45e1c-144">CentOS</span><span class="sxs-lookup"><span data-stu-id="45e1c-144">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="45e1c-145">Debian</span><span class="sxs-lookup"><span data-stu-id="45e1c-145">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="45e1c-146">Fedora</span><span class="sxs-lookup"><span data-stu-id="45e1c-146">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="45e1c-147">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="45e1c-147">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="45e1c-148">macOS</span><span class="sxs-lookup"><span data-stu-id="45e1c-148">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="45e1c-149">Windows</span><span class="sxs-lookup"><span data-stu-id="45e1c-149">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="45e1c-150">Usługa Git</span><span class="sxs-lookup"><span data-stu-id="45e1c-150">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="45e1c-151">Pobieranie przykładowej aplikacji</span><span class="sxs-lookup"><span data-stu-id="45e1c-151">Download the sample app</span></span>

* <span data-ttu-id="45e1c-152">Pobierz próbkę, usuwając [repozytorium .NET Docker](https://github.com/dotnet/dotnet-docker):</span><span class="sxs-lookup"><span data-stu-id="45e1c-152">Download the sample by cloning the [.NET Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="45e1c-153">Lokalne uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="45e1c-153">Run the app locally</span></span>

* <span data-ttu-id="45e1c-154">Przejdź do folderu projektu w programie *dotnet-Docker/Samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="45e1c-154">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="45e1c-155">Uruchom następujące polecenie, aby skompilować i uruchomić aplikację lokalnie:</span><span class="sxs-lookup"><span data-stu-id="45e1c-155">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="45e1c-156">Przejdź do programu `http://localhost:5000` w przeglądarce, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="45e1c-156">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="45e1c-157">Naciśnij klawisze CTRL + C w wierszu polecenia, aby zatrzymać aplikację.</span><span class="sxs-lookup"><span data-stu-id="45e1c-157">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="45e1c-158">Uruchamianie w kontenerze systemu Linux</span><span class="sxs-lookup"><span data-stu-id="45e1c-158">Run in a Linux container</span></span>

* <span data-ttu-id="45e1c-159">W kliencie platformy Docker [Przejdź do obszaru kontenery systemu Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span><span class="sxs-lookup"><span data-stu-id="45e1c-159">In the Docker client, [switch to Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

* <span data-ttu-id="45e1c-160">Przejdź do folderu pliku dockerfile w programie *dotnet-Docker/Samples/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="45e1c-160">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="45e1c-161">Uruchom następujące polecenia, aby skompilować i uruchomić przykład w Docker:</span><span class="sxs-lookup"><span data-stu-id="45e1c-161">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="45e1c-162">`build`Argumenty polecenia:</span><span class="sxs-lookup"><span data-stu-id="45e1c-162">The `build` command arguments:</span></span>
  * <span data-ttu-id="45e1c-163">Nazwij obraz aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="45e1c-163">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="45e1c-164">Wyszukaj pliku dockerfile w bieżącym folderze (okres na końcu).</span><span class="sxs-lookup"><span data-stu-id="45e1c-164">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="45e1c-165">Argumenty polecenia Run:</span><span class="sxs-lookup"><span data-stu-id="45e1c-165">The run command arguments:</span></span>
  * <span data-ttu-id="45e1c-166">Przydziel pseudo-TTY i pozostaw go otwarty nawet wtedy, gdy nie jest dołączony.</span><span class="sxs-lookup"><span data-stu-id="45e1c-166">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="45e1c-167">(Ten sam efekt jako `--interactive --tty` )</span><span class="sxs-lookup"><span data-stu-id="45e1c-167">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="45e1c-168">Automatycznie Usuń kontener, gdy zostanie on zakończony.</span><span class="sxs-lookup"><span data-stu-id="45e1c-168">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="45e1c-169">Mapuj port 5000 na komputerze lokalnym na port 80 w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="45e1c-169">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="45e1c-170">Nadaj nazwę kontenerowi aspnetcore_sample.</span><span class="sxs-lookup"><span data-stu-id="45e1c-170">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="45e1c-171">Określ obraz aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="45e1c-171">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="45e1c-172">Przejdź do programu `http://localhost:5000` w przeglądarce, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="45e1c-172">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="45e1c-173">Uruchamianie w kontenerze systemu Windows</span><span class="sxs-lookup"><span data-stu-id="45e1c-173">Run in a Windows container</span></span>

* <span data-ttu-id="45e1c-174">W kliencie platformy Docker [Przejdź do kontenerów systemu Windows](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span><span class="sxs-lookup"><span data-stu-id="45e1c-174">In the Docker client, [switch to Windows containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

<span data-ttu-id="45e1c-175">Przejdź do folderu pliku platformy Docker pod adresem `dotnet-docker/samples/aspnetapp` .</span><span class="sxs-lookup"><span data-stu-id="45e1c-175">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="45e1c-176">Uruchom następujące polecenia, aby skompilować i uruchomić przykład w Docker:</span><span class="sxs-lookup"><span data-stu-id="45e1c-176">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="45e1c-177">W przypadku kontenerów systemu Windows wymagany jest adres IP kontenera (przeglądanie w celu `http://localhost:5000` uniemożliwienia pracy):</span><span class="sxs-lookup"><span data-stu-id="45e1c-177">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="45e1c-178">Otwórz inny wiersz polecenia.</span><span class="sxs-lookup"><span data-stu-id="45e1c-178">Open up another command prompt.</span></span>
  * <span data-ttu-id="45e1c-179">Uruchom, `docker ps` Aby wyświetlić uruchomione kontenery.</span><span class="sxs-lookup"><span data-stu-id="45e1c-179">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="45e1c-180">Upewnij się, że kontener "aspnetcore_sample" znajduje się w tym miejscu.</span><span class="sxs-lookup"><span data-stu-id="45e1c-180">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="45e1c-181">Uruchom, `docker exec aspnetcore_sample ipconfig` Aby wyświetlić adres IP kontenera.</span><span class="sxs-lookup"><span data-stu-id="45e1c-181">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="45e1c-182">Dane wyjściowe polecenia wyglądają jak w tym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="45e1c-182">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="45e1c-183">Skopiuj adres IPv4 kontenera (na przykład 172.29.245.43) i wklej go na pasku adresu przeglądarki, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="45e1c-183">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="45e1c-184">Kompiluj i wdrażaj ręcznie</span><span class="sxs-lookup"><span data-stu-id="45e1c-184">Build and deploy manually</span></span>

<span data-ttu-id="45e1c-185">W niektórych scenariuszach może zajść potrzeba wdrożenia aplikacji w kontenerze przez skopiowanie jej zasobów wymaganych w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="45e1c-185">In some scenarios, you might want to deploy an app to a container by copying its assets that are needed at run time.</span></span> <span data-ttu-id="45e1c-186">W tej sekcji pokazano, jak wdrożyć ręcznie.</span><span class="sxs-lookup"><span data-stu-id="45e1c-186">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="45e1c-187">Przejdź do folderu projektu w programie *dotnet-Docker/Samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="45e1c-187">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="45e1c-188">Uruchom [dotnet Publish](/dotnet/core/tools/dotnet-publish) polecenie:</span><span class="sxs-lookup"><span data-stu-id="45e1c-188">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="45e1c-189">Argumenty polecenia:</span><span class="sxs-lookup"><span data-stu-id="45e1c-189">The command arguments:</span></span>
  * <span data-ttu-id="45e1c-190">Kompiluj aplikację w trybie wydania (domyślnie jest to tryb debugowania).</span><span class="sxs-lookup"><span data-stu-id="45e1c-190">Build the app in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="45e1c-191">Utwórz zasoby w folderze *opublikowanym* .</span><span class="sxs-lookup"><span data-stu-id="45e1c-191">Create the assets in the *published* folder.</span></span>

* <span data-ttu-id="45e1c-192">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="45e1c-192">Run the app.</span></span>

  * <span data-ttu-id="45e1c-193">W systemie Windows:</span><span class="sxs-lookup"><span data-stu-id="45e1c-193">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="45e1c-194">W systemie Linux:</span><span class="sxs-lookup"><span data-stu-id="45e1c-194">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="45e1c-195">Przejdź do `http://localhost:5000` strony głównej.</span><span class="sxs-lookup"><span data-stu-id="45e1c-195">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="45e1c-196">Aby użyć ręcznie opublikowanej aplikacji w kontenerze platformy Docker, Utwórz nowy *pliku dockerfile* i Użyj `docker build .` polecenia, aby skompilować obraz.</span><span class="sxs-lookup"><span data-stu-id="45e1c-196">To use the manually published app within a Docker container, create a new *Dockerfile* and use the `docker build .` command to build an image.</span></span>

::: moniker range=">= aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="45e1c-197">Aby wyświetlić nowy obraz, użyj `docker images` polecenia.</span><span class="sxs-lookup"><span data-stu-id="45e1c-197">To see the new image use the `docker images` command.</span></span>

### <a name="the-dockerfile"></a><span data-ttu-id="45e1c-198">Pliku dockerfile</span><span class="sxs-lookup"><span data-stu-id="45e1c-198">The Dockerfile</span></span>

<span data-ttu-id="45e1c-199">Oto *pliku dockerfile* używany przez `docker build` wykonane wcześniej polecenie.</span><span class="sxs-lookup"><span data-stu-id="45e1c-199">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="45e1c-200">Używa tego `dotnet publish` samego sposobu tworzenia i wdrażania w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="45e1c-200">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
# https://hub.docker.com/_/microsoft-dotnet
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /source

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /source/aspnetapp
RUN dotnet publish -c release -o /app --no-restore

# final stage/image
FROM mcr.microsoft.com/dotnet/aspnet:5.0
WORKDIR /app
COPY --from=build /app ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="45e1c-201">W poprzednim *pliku dockerfile* `*.csproj` pliki są kopiowane i przywracane jako odrębne *warstwy*.</span><span class="sxs-lookup"><span data-stu-id="45e1c-201">In the preceding *Dockerfile*, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="45e1c-202">Gdy `docker build` polecenie kompiluje obraz, używa wbudowanej pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="45e1c-202">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="45e1c-203">Jeśli `*.csproj` pliki nie uległy zmianie od czasu `docker build` ostatniego uruchomienia polecenia, `dotnet restore` nie trzeba ponownie uruchamiać polecenia.</span><span class="sxs-lookup"><span data-stu-id="45e1c-203">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="45e1c-204">Zamiast tego Wbudowana pamięć podręczna dla odpowiedniej `dotnet restore` warstwy jest używana ponownie.</span><span class="sxs-lookup"><span data-stu-id="45e1c-204">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="45e1c-205">Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dotyczące pisania wieloetapowe dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span><span class="sxs-lookup"><span data-stu-id="45e1c-205">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="45e1c-206">Pliku dockerfile</span><span class="sxs-lookup"><span data-stu-id="45e1c-206">The Dockerfile</span></span>

<span data-ttu-id="45e1c-207">Oto *pliku dockerfile* używany przez `docker build` wykonane wcześniej polecenie.</span><span class="sxs-lookup"><span data-stu-id="45e1c-207">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="45e1c-208">Używa tego `dotnet publish` samego sposobu tworzenia i wdrażania w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="45e1c-208">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:3.0 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="45e1c-209">Jak wspomniano w poprzednim pliku dockerfile, `*.csproj` pliki są kopiowane i przywracane jako odrębne *warstwy*.</span><span class="sxs-lookup"><span data-stu-id="45e1c-209">As noted in the preceding Dockerfile, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="45e1c-210">Gdy `docker build` polecenie kompiluje obraz, używa wbudowanej pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="45e1c-210">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="45e1c-211">Jeśli `*.csproj` pliki nie uległy zmianie od czasu `docker build` ostatniego uruchomienia polecenia, `dotnet restore` nie trzeba ponownie uruchamiać polecenia.</span><span class="sxs-lookup"><span data-stu-id="45e1c-211">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="45e1c-212">Zamiast tego Wbudowana pamięć podręczna dla odpowiedniej `dotnet restore` warstwy jest używana ponownie.</span><span class="sxs-lookup"><span data-stu-id="45e1c-212">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="45e1c-213">Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dotyczące pisania wieloetapowe dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span><span class="sxs-lookup"><span data-stu-id="45e1c-213">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="45e1c-214">Pliku dockerfile</span><span class="sxs-lookup"><span data-stu-id="45e1c-214">The Dockerfile</span></span>

<span data-ttu-id="45e1c-215">Oto *pliku dockerfile* używany przez `docker build` wykonane wcześniej polecenie.</span><span class="sxs-lookup"><span data-stu-id="45e1c-215">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span> <span data-ttu-id="45e1c-216">Używa tego `dotnet publish` samego sposobu tworzenia i wdrażania w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="45e1c-216">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="45e1c-217">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="45e1c-217">Additional resources</span></span>

* [<span data-ttu-id="45e1c-218">Docker Build — polecenie</span><span class="sxs-lookup"><span data-stu-id="45e1c-218">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="45e1c-219">Polecenie Docker Run</span><span class="sxs-lookup"><span data-stu-id="45e1c-219">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="45e1c-220">[ASP.NET Core Docker — przykład](https://github.com/dotnet/dotnet-docker) (używany w tym samouczku).</span><span class="sxs-lookup"><span data-stu-id="45e1c-220">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="45e1c-221">Konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="45e1c-221">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](../proxy-load-balancer.md)
* [<span data-ttu-id="45e1c-222">Praca z narzędziami platformy Docker programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="45e1c-222">Working with Visual Studio Docker Tools</span></span>](./visual-studio-tools-for-docker.md)
* [<span data-ttu-id="45e1c-223">Debugowanie za pomocą Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="45e1c-223">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [<span data-ttu-id="45e1c-224">GC przy użyciu platformy Docker i małych kontenerów</span><span class="sxs-lookup"><span data-stu-id="45e1c-224">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

## <a name="next-steps"></a><span data-ttu-id="45e1c-225">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="45e1c-225">Next steps</span></span>

<span data-ttu-id="45e1c-226">Repozytorium git zawierające przykładową aplikację zawiera również dokumentację.</span><span class="sxs-lookup"><span data-stu-id="45e1c-226">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="45e1c-227">Aby zapoznać się z omówieniem zasobów dostępnych w repozytorium, zobacz [plik Readme](https://github.com/dotnet/dotnet-docker/blob/main/samples/aspnetapp/README.md).</span><span class="sxs-lookup"><span data-stu-id="45e1c-227">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/main/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="45e1c-228">W szczególności Dowiedz się, jak zaimplementować protokół HTTPS:</span><span class="sxs-lookup"><span data-stu-id="45e1c-228">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="45e1c-229">Opracowywanie aplikacji ASP.NET Core przy użyciu platformy Docker za pośrednictwem protokołu HTTPS</span><span class="sxs-lookup"><span data-stu-id="45e1c-229">Developing ASP.NET Core Applications with Docker over HTTPS</span></span>](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md)
