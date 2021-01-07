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
ms.openlocfilehash: 2cd21722082af88e536bc1001b606ee96e7cf59b
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972057"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="3639a-104">Obrazy platformy Docker dla ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3639a-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="3639a-105">W tym samouczku pokazano, jak uruchomić aplikację ASP.NET Core w kontenerach platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="3639a-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="3639a-106">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="3639a-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="3639a-107">Dowiedz się więcej na temat ASP.NET Core obrazów platformy Docker</span><span class="sxs-lookup"><span data-stu-id="3639a-107">Learn about ASP.NET Core Docker images</span></span>
> * <span data-ttu-id="3639a-108">Pobieranie przykładowej aplikacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3639a-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="3639a-109">Uruchamianie przykładowej aplikacji lokalnie</span><span class="sxs-lookup"><span data-stu-id="3639a-109">Run the sample app locally</span></span>
> * <span data-ttu-id="3639a-110">Uruchamianie przykładowej aplikacji w kontenerach systemu Linux</span><span class="sxs-lookup"><span data-stu-id="3639a-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="3639a-111">Uruchamianie przykładowej aplikacji w kontenerach systemu Windows</span><span class="sxs-lookup"><span data-stu-id="3639a-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="3639a-112">Kompiluj i wdrażaj ręcznie</span><span class="sxs-lookup"><span data-stu-id="3639a-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="3639a-113">ASP.NET Core obrazów platformy Docker</span><span class="sxs-lookup"><span data-stu-id="3639a-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="3639a-114">Na potrzeby tego samouczka pobierzesz przykładową aplikację ASP.NET Core i uruchomisz ją w kontenerach platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="3639a-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="3639a-115">Przykład działa w przypadku kontenerów systemów Linux i Windows.</span><span class="sxs-lookup"><span data-stu-id="3639a-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="3639a-116">Przykładowy pliku dockerfile używa [funkcji budowania wielu etapów platformy Docker](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) do kompilowania i uruchamiania w różnych kontenerach.</span><span class="sxs-lookup"><span data-stu-id="3639a-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="3639a-117">Kontenery kompilacji i uruchamiania są tworzone na podstawie obrazów udostępnianych w usłudze Docker Hub przez firmę Microsoft:</span><span class="sxs-lookup"><span data-stu-id="3639a-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

::: moniker range=">= aspnetcore-5.0"

* `dotnet/sdk`

  <span data-ttu-id="3639a-118">Przykład używa tego obrazu do kompilowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3639a-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="3639a-119">Obraz zawiera zestaw .NET SDK, który zawiera narzędzia wiersza polecenia (CLI).</span><span class="sxs-lookup"><span data-stu-id="3639a-119">The image contains the .NET SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="3639a-120">Obraz jest zoptymalizowany pod kątem lokalnego projektowania, debugowania i testowania jednostkowego.</span><span class="sxs-lookup"><span data-stu-id="3639a-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="3639a-121">Narzędzia zainstalowane do programowania i kompilowania sprawiają, że obraz jest stosunkowo duży.</span><span class="sxs-lookup"><span data-stu-id="3639a-121">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/sdk`

  <span data-ttu-id="3639a-122">Przykład używa tego obrazu do kompilowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3639a-122">The sample uses this image for building the app.</span></span> <span data-ttu-id="3639a-123">Obraz zawiera zestaw .NET Core SDK, który zawiera narzędzia wiersza polecenia (CLI).</span><span class="sxs-lookup"><span data-stu-id="3639a-123">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="3639a-124">Obraz jest zoptymalizowany pod kątem lokalnego projektowania, debugowania i testowania jednostkowego.</span><span class="sxs-lookup"><span data-stu-id="3639a-124">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="3639a-125">Narzędzia zainstalowane do programowania i kompilowania sprawiają, że obraz jest stosunkowo duży.</span><span class="sxs-lookup"><span data-stu-id="3639a-125">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `dotnet/aspnet`

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/aspnet`

::: moniker-end

   <span data-ttu-id="3639a-126">Przykład używa tego obrazu do uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3639a-126">The sample uses this image for running the app.</span></span> <span data-ttu-id="3639a-127">Obraz zawiera ASP.NET Core środowiska uruchomieniowego i bibliotek, które są zoptymalizowane pod kątem uruchamiania aplikacji w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="3639a-127">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="3639a-128">Obraz jest stosunkowo mały, zaprojektowany z myślą o szybkości wdrażania i uruchamiania aplikacji, dlatego Optymalizacja wydajności sieci z poziomu rejestru platformy Docker do hosta platformy Docker jest zoptymalizowana.</span><span class="sxs-lookup"><span data-stu-id="3639a-128">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="3639a-129">Tylko pliki binarne i zawartość, które są konieczne do uruchomienia aplikacji, są kopiowane do kontenera.</span><span class="sxs-lookup"><span data-stu-id="3639a-129">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="3639a-130">Zawartość jest gotowa do uruchomienia, co pozwoli na najszybszy czas od `docker run` do uruchomienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3639a-130">The contents are ready to run, enabling the fastest time from `docker run` to app startup.</span></span> <span data-ttu-id="3639a-131">W modelu platformy Docker nie jest wymagana kompilacja kodu dynamicznego.</span><span class="sxs-lookup"><span data-stu-id="3639a-131">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3639a-132">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="3639a-132">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

* [<span data-ttu-id="3639a-133">.NET SDK 5.0</span><span class="sxs-lookup"><span data-stu-id="3639a-133">.NET SDK 5.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* [<span data-ttu-id="3639a-134">Zestaw .NET Core SDK 3.1</span><span class="sxs-lookup"><span data-stu-id="3639a-134">.NET Core SDK 3.1</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="3639a-135">Zestaw SDK platformy .NET Core 2,2</span><span class="sxs-lookup"><span data-stu-id="3639a-135">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

::: moniker-end

* <span data-ttu-id="3639a-136">Klient platformy Docker 18,03 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="3639a-136">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="3639a-137">Dystrybucje systemu Linux</span><span class="sxs-lookup"><span data-stu-id="3639a-137">Linux distributions</span></span>
    * [<span data-ttu-id="3639a-138">CentOS</span><span class="sxs-lookup"><span data-stu-id="3639a-138">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="3639a-139">Debian</span><span class="sxs-lookup"><span data-stu-id="3639a-139">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="3639a-140">Fedora</span><span class="sxs-lookup"><span data-stu-id="3639a-140">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="3639a-141">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="3639a-141">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="3639a-142">macOS</span><span class="sxs-lookup"><span data-stu-id="3639a-142">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="3639a-143">Windows</span><span class="sxs-lookup"><span data-stu-id="3639a-143">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="3639a-144">Narzędzia</span><span class="sxs-lookup"><span data-stu-id="3639a-144">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="3639a-145">Pobieranie przykładowej aplikacji</span><span class="sxs-lookup"><span data-stu-id="3639a-145">Download the sample app</span></span>

* <span data-ttu-id="3639a-146">Pobierz próbkę, usuwając [repozytorium .NET Docker](https://github.com/dotnet/dotnet-docker):</span><span class="sxs-lookup"><span data-stu-id="3639a-146">Download the sample by cloning the [.NET Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="3639a-147">Lokalne uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="3639a-147">Run the app locally</span></span>

* <span data-ttu-id="3639a-148">Przejdź do folderu projektu w programie *dotnet-Docker/Samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="3639a-148">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="3639a-149">Uruchom następujące polecenie, aby skompilować i uruchomić aplikację lokalnie:</span><span class="sxs-lookup"><span data-stu-id="3639a-149">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="3639a-150">Przejdź do programu `http://localhost:5000` w przeglądarce, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="3639a-150">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="3639a-151">Naciśnij klawisze CTRL + C w wierszu polecenia, aby zatrzymać aplikację.</span><span class="sxs-lookup"><span data-stu-id="3639a-151">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="3639a-152">Uruchamianie w kontenerze systemu Linux</span><span class="sxs-lookup"><span data-stu-id="3639a-152">Run in a Linux container</span></span>

* <span data-ttu-id="3639a-153">W kliencie platformy Docker [Przejdź do obszaru kontenery systemu Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span><span class="sxs-lookup"><span data-stu-id="3639a-153">In the Docker client, [switch to Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

* <span data-ttu-id="3639a-154">Przejdź do folderu pliku dockerfile w programie *dotnet-Docker/Samples/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="3639a-154">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="3639a-155">Uruchom następujące polecenia, aby skompilować i uruchomić przykład w Docker:</span><span class="sxs-lookup"><span data-stu-id="3639a-155">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="3639a-156">`build`Argumenty polecenia:</span><span class="sxs-lookup"><span data-stu-id="3639a-156">The `build` command arguments:</span></span>
  * <span data-ttu-id="3639a-157">Nazwij obraz aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="3639a-157">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="3639a-158">Wyszukaj pliku dockerfile w bieżącym folderze (okres na końcu).</span><span class="sxs-lookup"><span data-stu-id="3639a-158">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="3639a-159">Argumenty polecenia Run:</span><span class="sxs-lookup"><span data-stu-id="3639a-159">The run command arguments:</span></span>
  * <span data-ttu-id="3639a-160">Przydziel pseudo-TTY i pozostaw go otwarty nawet wtedy, gdy nie jest dołączony.</span><span class="sxs-lookup"><span data-stu-id="3639a-160">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="3639a-161">(Ten sam efekt jako `--interactive --tty` )</span><span class="sxs-lookup"><span data-stu-id="3639a-161">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="3639a-162">Automatycznie Usuń kontener, gdy zostanie on zakończony.</span><span class="sxs-lookup"><span data-stu-id="3639a-162">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="3639a-163">Mapuj port 5000 na komputerze lokalnym na port 80 w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="3639a-163">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="3639a-164">Nadaj nazwę kontenerowi aspnetcore_sample.</span><span class="sxs-lookup"><span data-stu-id="3639a-164">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="3639a-165">Określ obraz aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="3639a-165">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="3639a-166">Przejdź do programu `http://localhost:5000` w przeglądarce, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="3639a-166">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="3639a-167">Uruchamianie w kontenerze systemu Windows</span><span class="sxs-lookup"><span data-stu-id="3639a-167">Run in a Windows container</span></span>

* <span data-ttu-id="3639a-168">W kliencie platformy Docker [Przejdź do kontenerów systemu Windows](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span><span class="sxs-lookup"><span data-stu-id="3639a-168">In the Docker client, [switch to Windows containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

<span data-ttu-id="3639a-169">Przejdź do folderu pliku platformy Docker pod adresem `dotnet-docker/samples/aspnetapp` .</span><span class="sxs-lookup"><span data-stu-id="3639a-169">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="3639a-170">Uruchom następujące polecenia, aby skompilować i uruchomić przykład w Docker:</span><span class="sxs-lookup"><span data-stu-id="3639a-170">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="3639a-171">W przypadku kontenerów systemu Windows wymagany jest adres IP kontenera (przeglądanie w celu `http://localhost:5000` uniemożliwienia pracy):</span><span class="sxs-lookup"><span data-stu-id="3639a-171">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="3639a-172">Otwórz inny wiersz polecenia.</span><span class="sxs-lookup"><span data-stu-id="3639a-172">Open up another command prompt.</span></span>
  * <span data-ttu-id="3639a-173">Uruchom, `docker ps` Aby wyświetlić uruchomione kontenery.</span><span class="sxs-lookup"><span data-stu-id="3639a-173">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="3639a-174">Upewnij się, że kontener "aspnetcore_sample" znajduje się w tym miejscu.</span><span class="sxs-lookup"><span data-stu-id="3639a-174">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="3639a-175">Uruchom, `docker exec aspnetcore_sample ipconfig` Aby wyświetlić adres IP kontenera.</span><span class="sxs-lookup"><span data-stu-id="3639a-175">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="3639a-176">Dane wyjściowe polecenia wyglądają jak w tym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="3639a-176">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="3639a-177">Skopiuj adres IPv4 kontenera (na przykład 172.29.245.43) i wklej go na pasku adresu przeglądarki, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="3639a-177">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="3639a-178">Kompiluj i wdrażaj ręcznie</span><span class="sxs-lookup"><span data-stu-id="3639a-178">Build and deploy manually</span></span>

<span data-ttu-id="3639a-179">W niektórych scenariuszach może zajść potrzeba wdrożenia aplikacji w kontenerze przez skopiowanie jej zasobów wymaganych w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="3639a-179">In some scenarios, you might want to deploy an app to a container by copying its assets that are needed at run time.</span></span> <span data-ttu-id="3639a-180">W tej sekcji pokazano, jak wdrożyć ręcznie.</span><span class="sxs-lookup"><span data-stu-id="3639a-180">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="3639a-181">Przejdź do folderu projektu w programie *dotnet-Docker/Samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="3639a-181">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="3639a-182">Uruchom [dotnet Publish](/dotnet/core/tools/dotnet-publish) polecenie:</span><span class="sxs-lookup"><span data-stu-id="3639a-182">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="3639a-183">Argumenty polecenia:</span><span class="sxs-lookup"><span data-stu-id="3639a-183">The command arguments:</span></span>
  * <span data-ttu-id="3639a-184">Kompiluj aplikację w trybie wydania (domyślnie jest to tryb debugowania).</span><span class="sxs-lookup"><span data-stu-id="3639a-184">Build the app in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="3639a-185">Utwórz zasoby w folderze *opublikowanym* .</span><span class="sxs-lookup"><span data-stu-id="3639a-185">Create the assets in the *published* folder.</span></span>

* <span data-ttu-id="3639a-186">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="3639a-186">Run the app.</span></span>

  * <span data-ttu-id="3639a-187">W systemie Windows:</span><span class="sxs-lookup"><span data-stu-id="3639a-187">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="3639a-188">W systemie Linux:</span><span class="sxs-lookup"><span data-stu-id="3639a-188">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="3639a-189">Przejdź do `http://localhost:5000` strony głównej.</span><span class="sxs-lookup"><span data-stu-id="3639a-189">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="3639a-190">Aby użyć ręcznie opublikowanej aplikacji w kontenerze platformy Docker, Utwórz nowy *pliku dockerfile* i Użyj `docker build .` polecenia, aby skompilować kontener.</span><span class="sxs-lookup"><span data-stu-id="3639a-190">To use the manually published app within a Docker container, create a new *Dockerfile* and use the `docker build .` command to build the container.</span></span>

::: moniker range=">= aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="3639a-191">Pliku dockerfile</span><span class="sxs-lookup"><span data-stu-id="3639a-191">The Dockerfile</span></span>

<span data-ttu-id="3639a-192">Oto *pliku dockerfile* używany przez `docker build` wykonane wcześniej polecenie.</span><span class="sxs-lookup"><span data-stu-id="3639a-192">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="3639a-193">Używa tego `dotnet publish` samego sposobu tworzenia i wdrażania w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="3639a-193">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

<span data-ttu-id="3639a-194">W poprzednim *pliku dockerfile* `*.csproj` pliki są kopiowane i przywracane jako odrębne *warstwy*.</span><span class="sxs-lookup"><span data-stu-id="3639a-194">In the preceding *Dockerfile*, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="3639a-195">Gdy `docker build` polecenie kompiluje obraz, używa wbudowanej pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="3639a-195">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="3639a-196">Jeśli `*.csproj` pliki nie uległy zmianie od czasu `docker build` ostatniego uruchomienia polecenia, `dotnet restore` nie trzeba ponownie uruchamiać polecenia.</span><span class="sxs-lookup"><span data-stu-id="3639a-196">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="3639a-197">Zamiast tego Wbudowana pamięć podręczna dla odpowiedniej `dotnet restore` warstwy jest używana ponownie.</span><span class="sxs-lookup"><span data-stu-id="3639a-197">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="3639a-198">Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dotyczące pisania wieloetapowe dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span><span class="sxs-lookup"><span data-stu-id="3639a-198">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="3639a-199">Pliku dockerfile</span><span class="sxs-lookup"><span data-stu-id="3639a-199">The Dockerfile</span></span>

<span data-ttu-id="3639a-200">Oto *pliku dockerfile* używany przez `docker build` wykonane wcześniej polecenie.</span><span class="sxs-lookup"><span data-stu-id="3639a-200">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="3639a-201">Używa tego `dotnet publish` samego sposobu tworzenia i wdrażania w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="3639a-201">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

<span data-ttu-id="3639a-202">Jak wspomniano w poprzednim pliku dockerfile, `*.csproj` pliki są kopiowane i przywracane jako odrębne *warstwy*.</span><span class="sxs-lookup"><span data-stu-id="3639a-202">As noted in the preceding Dockerfile, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="3639a-203">Gdy `docker build` polecenie kompiluje obraz, używa wbudowanej pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="3639a-203">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="3639a-204">Jeśli `*.csproj` pliki nie uległy zmianie od czasu `docker build` ostatniego uruchomienia polecenia, `dotnet restore` nie trzeba ponownie uruchamiać polecenia.</span><span class="sxs-lookup"><span data-stu-id="3639a-204">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="3639a-205">Zamiast tego Wbudowana pamięć podręczna dla odpowiedniej `dotnet restore` warstwy jest używana ponownie.</span><span class="sxs-lookup"><span data-stu-id="3639a-205">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="3639a-206">Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dotyczące pisania wieloetapowe dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span><span class="sxs-lookup"><span data-stu-id="3639a-206">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="3639a-207">Pliku dockerfile</span><span class="sxs-lookup"><span data-stu-id="3639a-207">The Dockerfile</span></span>

<span data-ttu-id="3639a-208">Oto *pliku dockerfile* używany przez `docker build` wykonane wcześniej polecenie.</span><span class="sxs-lookup"><span data-stu-id="3639a-208">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span> <span data-ttu-id="3639a-209">Używa tego `dotnet publish` samego sposobu tworzenia i wdrażania w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="3639a-209">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

## <a name="additional-resources"></a><span data-ttu-id="3639a-210">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="3639a-210">Additional resources</span></span>

* [<span data-ttu-id="3639a-211">Docker Build — polecenie</span><span class="sxs-lookup"><span data-stu-id="3639a-211">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="3639a-212">Polecenie Docker Run</span><span class="sxs-lookup"><span data-stu-id="3639a-212">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="3639a-213">[ASP.NET Core Docker — przykład](https://github.com/dotnet/dotnet-docker) (używany w tym samouczku).</span><span class="sxs-lookup"><span data-stu-id="3639a-213">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="3639a-214">Konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="3639a-214">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](../proxy-load-balancer.md)
* [<span data-ttu-id="3639a-215">Praca z narzędziami platformy Docker programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3639a-215">Working with Visual Studio Docker Tools</span></span>](./visual-studio-tools-for-docker.md)
* [<span data-ttu-id="3639a-216">Debugowanie za pomocą Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3639a-216">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [<span data-ttu-id="3639a-217">GC przy użyciu platformy Docker i małych kontenerów</span><span class="sxs-lookup"><span data-stu-id="3639a-217">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

## <a name="next-steps"></a><span data-ttu-id="3639a-218">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="3639a-218">Next steps</span></span>

<span data-ttu-id="3639a-219">Repozytorium git zawierające przykładową aplikację zawiera również dokumentację.</span><span class="sxs-lookup"><span data-stu-id="3639a-219">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="3639a-220">Aby zapoznać się z omówieniem zasobów dostępnych w repozytorium, zobacz [plik Readme](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span><span class="sxs-lookup"><span data-stu-id="3639a-220">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="3639a-221">W szczególności Dowiedz się, jak zaimplementować protokół HTTPS:</span><span class="sxs-lookup"><span data-stu-id="3639a-221">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="3639a-222">Opracowywanie aplikacji ASP.NET Core przy użyciu platformy Docker za pośrednictwem protokołu HTTPS</span><span class="sxs-lookup"><span data-stu-id="3639a-222">Developing ASP.NET Core Applications with Docker over HTTPS</span></span>](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)
