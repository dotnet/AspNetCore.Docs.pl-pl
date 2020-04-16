---
title: Obrazy platformy Docker dla ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak korzystać z opublikowanych obrazów platformy .NET Core Docker z rejestru platformy Docker. Ściągaj obrazy i twórz własne obrazy.
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2020
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: ced0cb7cbeed1b8811813a70035c2e0b42c3e35a
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440782"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="9b77c-104">Obrazy platformy Docker dla ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9b77c-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="9b77c-105">W tym samouczku pokazano, jak uruchomić aplikację ASP.NET Core w kontenerach platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="9b77c-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="9b77c-106">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="9b77c-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="9b77c-107">Dowiedz się więcej o obrazach platformy Docker platformy Microsoft .NET Core</span><span class="sxs-lookup"><span data-stu-id="9b77c-107">Learn about Microsoft .NET Core Docker images</span></span>
> * <span data-ttu-id="9b77c-108">Pobieranie przykładowej aplikacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9b77c-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="9b77c-109">Lokalne uruchamianie przykładowej aplikacji</span><span class="sxs-lookup"><span data-stu-id="9b77c-109">Run the sample app locally</span></span>
> * <span data-ttu-id="9b77c-110">Uruchamianie przykładowej aplikacji w kontenerach systemu Linux</span><span class="sxs-lookup"><span data-stu-id="9b77c-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="9b77c-111">Uruchamianie przykładowej aplikacji w kontenerach systemu Windows</span><span class="sxs-lookup"><span data-stu-id="9b77c-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="9b77c-112">Ręczne tworzenie i wdrażanie</span><span class="sxs-lookup"><span data-stu-id="9b77c-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="9b77c-113">ASP.NET obrazy platformy Docker Core</span><span class="sxs-lookup"><span data-stu-id="9b77c-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="9b77c-114">W tym samouczku pobierz ASP.NET przykładową aplikację Core i uruchom ją w kontenerach platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="9b77c-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="9b77c-115">Przykład działa z kontenerami linux i windows.</span><span class="sxs-lookup"><span data-stu-id="9b77c-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="9b77c-116">Przykładowy dockerfile używa [funkcji kompilacji wieloetapowej platformy Docker](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) do tworzenia i uruchamiania w różnych kontenerach.</span><span class="sxs-lookup"><span data-stu-id="9b77c-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="9b77c-117">Kontenery kompilacji i uruchamiania są tworzone na podstawie obrazów dostarczanych w centrum platformy Docker przez firmę Microsoft:</span><span class="sxs-lookup"><span data-stu-id="9b77c-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

* `dotnet/core/sdk`

  <span data-ttu-id="9b77c-118">Przykład używa tego obrazu do tworzenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9b77c-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="9b77c-119">Obraz zawiera zestaw SDK .NET Core, który zawiera narzędzia wiersza polecenia (CLI).</span><span class="sxs-lookup"><span data-stu-id="9b77c-119">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="9b77c-120">Obraz jest zoptymalizowany pod kątem lokalnego rozwoju, debugowania i testowania jednostkowego.</span><span class="sxs-lookup"><span data-stu-id="9b77c-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="9b77c-121">Narzędzia zainstalowane do tworzenia i kompilacji sprawiają, że jest to stosunkowo duży obraz.</span><span class="sxs-lookup"><span data-stu-id="9b77c-121">The tools installed for development and compilation make this a relatively large image.</span></span> 

* `dotnet/core/aspnet`

   <span data-ttu-id="9b77c-122">Przykład używa tego obrazu do uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9b77c-122">The sample uses this image for running the app.</span></span> <span data-ttu-id="9b77c-123">Obraz zawiera ASP.NET Core środowiska uruchomieniowego i bibliotek i jest zoptymalizowany pod kątem uruchamiania aplikacji w produkcji.</span><span class="sxs-lookup"><span data-stu-id="9b77c-123">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="9b77c-124">Zaprojektowany z myślą o szybkości wdrażania i uruchamianiu aplikacji, obraz jest stosunkowo mały, więc wydajność sieci z rejestru platformy Docker do hosta platformy Docker jest zoptymalizowana.</span><span class="sxs-lookup"><span data-stu-id="9b77c-124">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="9b77c-125">Tylko pliki binarne i zawartość potrzebna do uruchomienia aplikacji są kopiowane do kontenera.</span><span class="sxs-lookup"><span data-stu-id="9b77c-125">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="9b77c-126">Zawartość jest gotowa do uruchomienia, umożliwiając najszybszy czas od `Docker run` uruchomienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9b77c-126">The contents are ready to run, enabling the fastest time from `Docker run` to app startup.</span></span> <span data-ttu-id="9b77c-127">Kompilacja kodu dynamicznego nie jest potrzebna w modelu platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="9b77c-127">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9b77c-128">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="9b77c-128">Prerequisites</span></span>
::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="9b77c-129">.NET Core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="9b77c-129">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="9b77c-130">.NET Core SDK 3.0</span><span class="sxs-lookup"><span data-stu-id="9b77c-130">.NET Core SDK 3.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

* <span data-ttu-id="9b77c-131">Klient platformy Docker 18.03 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="9b77c-131">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="9b77c-132">Dystrybucje Linuksa</span><span class="sxs-lookup"><span data-stu-id="9b77c-132">Linux distributions</span></span>
    * [<span data-ttu-id="9b77c-133">CentOS</span><span class="sxs-lookup"><span data-stu-id="9b77c-133">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="9b77c-134">Debian</span><span class="sxs-lookup"><span data-stu-id="9b77c-134">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="9b77c-135">Fedora</span><span class="sxs-lookup"><span data-stu-id="9b77c-135">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="9b77c-136">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="9b77c-136">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="9b77c-137">macOS</span><span class="sxs-lookup"><span data-stu-id="9b77c-137">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="9b77c-138">Windows</span><span class="sxs-lookup"><span data-stu-id="9b77c-138">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="9b77c-139">Git</span><span class="sxs-lookup"><span data-stu-id="9b77c-139">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="9b77c-140">Pobieranie przykładowej aplikacji</span><span class="sxs-lookup"><span data-stu-id="9b77c-140">Download the sample app</span></span>

* <span data-ttu-id="9b77c-141">Pobierz próbkę, klonując [repozytorium platformy .NET Core Docker:](https://github.com/dotnet/dotnet-docker)</span><span class="sxs-lookup"><span data-stu-id="9b77c-141">Download the sample by cloning the [.NET Core Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="9b77c-142">Lokalne uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="9b77c-142">Run the app locally</span></span>

* <span data-ttu-id="9b77c-143">Przejdź do folderu projektu w *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="9b77c-143">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="9b77c-144">Uruchom następujące polecenie, aby skompilować i uruchomić aplikację lokalnie:</span><span class="sxs-lookup"><span data-stu-id="9b77c-144">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="9b77c-145">Przejdź `http://localhost:5000` do przeglądarki, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="9b77c-145">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="9b77c-146">Naciśnij klawisze Ctrl+C w wierszu polecenia, aby zatrzymać aplikację.</span><span class="sxs-lookup"><span data-stu-id="9b77c-146">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="9b77c-147">Uruchamianie w kontenerze systemu Linux</span><span class="sxs-lookup"><span data-stu-id="9b77c-147">Run in a Linux container</span></span>

* <span data-ttu-id="9b77c-148">W kliencie platformy Docker przełącz się na kontenery systemu Linux.</span><span class="sxs-lookup"><span data-stu-id="9b77c-148">In the Docker client, switch to Linux containers.</span></span>

* <span data-ttu-id="9b77c-149">Przejdź do folderu Dockerfile w *dotnet-docker/samples/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="9b77c-149">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="9b77c-150">Uruchom następujące polecenia, aby skompilować i uruchomić przykład w umiań Docker:</span><span class="sxs-lookup"><span data-stu-id="9b77c-150">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="9b77c-151">Argumenty `build` polecenia:</span><span class="sxs-lookup"><span data-stu-id="9b77c-151">The `build` command arguments:</span></span>
  * <span data-ttu-id="9b77c-152">Nazwij obraz aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="9b77c-152">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="9b77c-153">Poszukaj pliku Dockerfile w bieżącym folderze (kropka na końcu).</span><span class="sxs-lookup"><span data-stu-id="9b77c-153">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="9b77c-154">Argumenty polecenia uruchom:</span><span class="sxs-lookup"><span data-stu-id="9b77c-154">The run command arguments:</span></span>
  * <span data-ttu-id="9b77c-155">Przydziel pseudo-TTY i zachowaj go otwartego, nawet jeśli nie jest dołączony.</span><span class="sxs-lookup"><span data-stu-id="9b77c-155">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="9b77c-156">(Taki sam `--interactive --tty`efekt jak .)</span><span class="sxs-lookup"><span data-stu-id="9b77c-156">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="9b77c-157">Automatycznie usuń kontener po jego wyjściu.</span><span class="sxs-lookup"><span data-stu-id="9b77c-157">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="9b77c-158">Mapuj port 5000 na komputerze lokalnym, aby port 80 w kontenerze.</span><span class="sxs-lookup"><span data-stu-id="9b77c-158">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="9b77c-159">Nazwij aspnetcore_sample kontenera.</span><span class="sxs-lookup"><span data-stu-id="9b77c-159">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="9b77c-160">Określ obraz aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="9b77c-160">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="9b77c-161">Przejdź `http://localhost:5000` do przeglądarki, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="9b77c-161">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="9b77c-162">Uruchamianie w kontenerze systemu Windows</span><span class="sxs-lookup"><span data-stu-id="9b77c-162">Run in a Windows container</span></span>

* <span data-ttu-id="9b77c-163">W kliencie platformy Docker przełącz się do kontenerów systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="9b77c-163">In the Docker client, switch to Windows containers.</span></span>

<span data-ttu-id="9b77c-164">Przejdź do folderu plików `dotnet-docker/samples/aspnetapp`platformy docker w pliku .</span><span class="sxs-lookup"><span data-stu-id="9b77c-164">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="9b77c-165">Uruchom następujące polecenia, aby skompilować i uruchomić przykład w umiań Docker:</span><span class="sxs-lookup"><span data-stu-id="9b77c-165">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="9b77c-166">W przypadku kontenerów systemu Windows potrzebny jest adres `http://localhost:5000` IP kontenera (przeglądanie, aby nie działać):</span><span class="sxs-lookup"><span data-stu-id="9b77c-166">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="9b77c-167">Otwórz inny wiersz polecenia.</span><span class="sxs-lookup"><span data-stu-id="9b77c-167">Open up another command prompt.</span></span>
  * <span data-ttu-id="9b77c-168">Uruchom, `docker ps` aby wyświetlić uruchomione kontenery.</span><span class="sxs-lookup"><span data-stu-id="9b77c-168">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="9b77c-169">Sprawdź, czy istnieje kontener "aspnetcore_sample".</span><span class="sxs-lookup"><span data-stu-id="9b77c-169">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="9b77c-170">Uruchom, `docker exec aspnetcore_sample ipconfig` aby wyświetlić adres IP kontenera.</span><span class="sxs-lookup"><span data-stu-id="9b77c-170">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="9b77c-171">Dane wyjściowe z polecenia wygląda następująco:</span><span class="sxs-lookup"><span data-stu-id="9b77c-171">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="9b77c-172">Skopiuj adres IPv4 kontenera (na przykład 172.29.245.43) i wklej go na pasku adresu przeglądarki, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="9b77c-172">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="9b77c-173">Ręczne tworzenie i wdrażanie</span><span class="sxs-lookup"><span data-stu-id="9b77c-173">Build and deploy manually</span></span>

<span data-ttu-id="9b77c-174">W niektórych scenariuszach można wdrożyć aplikację do kontenera, kopiując do niego pliki aplikacji, które są potrzebne w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="9b77c-174">In some scenarios, you might want to deploy an app to a container by copying to it the application files that are needed at run time.</span></span> <span data-ttu-id="9b77c-175">W tej sekcji pokazano, jak wdrożyć ręcznie.</span><span class="sxs-lookup"><span data-stu-id="9b77c-175">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="9b77c-176">Przejdź do folderu projektu w *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="9b77c-176">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="9b77c-177">Uruchom polecenie [publikowania dotnetu:](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="9b77c-177">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="9b77c-178">Argumenty polecenia:</span><span class="sxs-lookup"><span data-stu-id="9b77c-178">The command arguments:</span></span>
  * <span data-ttu-id="9b77c-179">Tworzenie aplikacji w trybie zwalniania (domyślnym jest tryb debugowania).</span><span class="sxs-lookup"><span data-stu-id="9b77c-179">Build the application in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="9b77c-180">Utwórz pliki w *opublikowanym folderze.*</span><span class="sxs-lookup"><span data-stu-id="9b77c-180">Create the files in the *published* folder.</span></span>

* <span data-ttu-id="9b77c-181">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="9b77c-181">Run the application.</span></span>

  * <span data-ttu-id="9b77c-182">W systemie Windows:</span><span class="sxs-lookup"><span data-stu-id="9b77c-182">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="9b77c-183">W systemie Linux:</span><span class="sxs-lookup"><span data-stu-id="9b77c-183">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="9b77c-184">Przejdź do `http://localhost:5000` strony głównej.</span><span class="sxs-lookup"><span data-stu-id="9b77c-184">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="9b77c-185">Aby użyć ręcznie opublikowanej aplikacji w kontenerze platformy Docker, utwórz `docker build .` nowy plik Dockerfile i użyj polecenia do utworzenia kontenera.</span><span class="sxs-lookup"><span data-stu-id="9b77c-185">To use the manually published application within a Docker container, create a new Dockerfile and use the `docker build .` command to build the container.</span></span>

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="9b77c-186">Plik dockerfile</span><span class="sxs-lookup"><span data-stu-id="9b77c-186">The Dockerfile</span></span>

<span data-ttu-id="9b77c-187">Oto *Dockerfile* używane przez `docker build` polecenie, które uruchomiono wcześniej.</span><span class="sxs-lookup"><span data-stu-id="9b77c-187">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="9b77c-188">Używa tak `dotnet publish` samo, jak w tej sekcji do tworzenia i wdrażania.</span><span class="sxs-lookup"><span data-stu-id="9b77c-188">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

::: moniker range=">= aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="9b77c-189">Plik dockerfile</span><span class="sxs-lookup"><span data-stu-id="9b77c-189">The Dockerfile</span></span>

<span data-ttu-id="9b77c-190">Oto *Dockerfile* używane przez `docker build` polecenie, które uruchomiono wcześniej.</span><span class="sxs-lookup"><span data-stu-id="9b77c-190">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="9b77c-191">Używa tak `dotnet publish` samo, jak w tej sekcji do tworzenia i wdrażania.</span><span class="sxs-lookup"><span data-stu-id="9b77c-191">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

::: moniker-end

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

## <a name="additional-resources"></a><span data-ttu-id="9b77c-192">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="9b77c-192">Additional resources</span></span>

* [<span data-ttu-id="9b77c-193">Polecenie kompilacji platformy Docker</span><span class="sxs-lookup"><span data-stu-id="9b77c-193">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="9b77c-194">Polecenie Uruchom docker</span><span class="sxs-lookup"><span data-stu-id="9b77c-194">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="9b77c-195">[ASP.NET próbka rdzenia platformy Docker](https://github.com/dotnet/dotnet-docker) (ten używany w tym samouczku).</span><span class="sxs-lookup"><span data-stu-id="9b77c-195">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="9b77c-196">Konfigurowanie ASP.NET Core do pracy z serwerami proxy i modułami równoważenia obciążenia</span><span class="sxs-lookup"><span data-stu-id="9b77c-196">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](/aspnet/core/host-and-deploy/proxy-load-balancer)
* [<span data-ttu-id="9b77c-197">Praca z narzędziami platformy Visual Studio docker</span><span class="sxs-lookup"><span data-stu-id="9b77c-197">Working with Visual Studio Docker Tools</span></span>](https://docs.microsoft.com/aspnet/core/publishing/visual-studio-tools-for-docker)
* [<span data-ttu-id="9b77c-198">Debugowanie za pomocą kodu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b77c-198">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [<span data-ttu-id="9b77c-199">GC przy użyciu platformy Docker i małych kontenerów</span><span class="sxs-lookup"><span data-stu-id="9b77c-199">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

## <a name="next-steps"></a><span data-ttu-id="9b77c-200">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="9b77c-200">Next steps</span></span>

<span data-ttu-id="9b77c-201">Repozytorium Git, które zawiera przykładową aplikację, zawiera również dokumentację.</span><span class="sxs-lookup"><span data-stu-id="9b77c-201">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="9b77c-202">Aby uzyskać przegląd zasobów dostępnych w repozytorium, zobacz [plik README](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span><span class="sxs-lookup"><span data-stu-id="9b77c-202">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="9b77c-203">W szczególności dowiedz się, jak zaimplementować protokół HTTPS:</span><span class="sxs-lookup"><span data-stu-id="9b77c-203">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="9b77c-204">Tworzenie ASP.NET podstawowych aplikacji za pomocą platformy Docker za pośrednictwem protokołu HTTPS</span><span class="sxs-lookup"><span data-stu-id="9b77c-204">Developing ASP.NET Core Applications with Docker over HTTPS</span></span>](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)
