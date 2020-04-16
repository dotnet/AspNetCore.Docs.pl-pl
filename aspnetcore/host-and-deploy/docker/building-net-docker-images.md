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
# <a name="docker-images-for-aspnet-core"></a>Obrazy platformy Docker dla ASP.NET Core

W tym samouczku pokazano, jak uruchomić aplikację ASP.NET Core w kontenerach platformy Docker.

W tym samouczku zostały wykonane następujące czynności:
> [!div class="checklist"]
> * Dowiedz się więcej o obrazach platformy Docker platformy Microsoft .NET Core
> * Pobieranie przykładowej aplikacji ASP.NET Core
> * Lokalne uruchamianie przykładowej aplikacji
> * Uruchamianie przykładowej aplikacji w kontenerach systemu Linux
> * Uruchamianie przykładowej aplikacji w kontenerach systemu Windows
> * Ręczne tworzenie i wdrażanie

## <a name="aspnet-core-docker-images"></a>ASP.NET obrazy platformy Docker Core

W tym samouczku pobierz ASP.NET przykładową aplikację Core i uruchom ją w kontenerach platformy Docker. Przykład działa z kontenerami linux i windows.

Przykładowy dockerfile używa [funkcji kompilacji wieloetapowej platformy Docker](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) do tworzenia i uruchamiania w różnych kontenerach. Kontenery kompilacji i uruchamiania są tworzone na podstawie obrazów dostarczanych w centrum platformy Docker przez firmę Microsoft:

* `dotnet/core/sdk`

  Przykład używa tego obrazu do tworzenia aplikacji. Obraz zawiera zestaw SDK .NET Core, który zawiera narzędzia wiersza polecenia (CLI). Obraz jest zoptymalizowany pod kątem lokalnego rozwoju, debugowania i testowania jednostkowego. Narzędzia zainstalowane do tworzenia i kompilacji sprawiają, że jest to stosunkowo duży obraz. 

* `dotnet/core/aspnet`

   Przykład używa tego obrazu do uruchamiania aplikacji. Obraz zawiera ASP.NET Core środowiska uruchomieniowego i bibliotek i jest zoptymalizowany pod kątem uruchamiania aplikacji w produkcji. Zaprojektowany z myślą o szybkości wdrażania i uruchamianiu aplikacji, obraz jest stosunkowo mały, więc wydajność sieci z rejestru platformy Docker do hosta platformy Docker jest zoptymalizowana. Tylko pliki binarne i zawartość potrzebna do uruchomienia aplikacji są kopiowane do kontenera. Zawartość jest gotowa do uruchomienia, umożliwiając najszybszy czas od `Docker run` uruchomienia aplikacji. Kompilacja kodu dynamicznego nie jest potrzebna w modelu platformy Docker.

## <a name="prerequisites"></a>Wymagania wstępne
::: moniker range="< aspnetcore-3.0"

* [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download/dotnet-core)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* [.NET Core SDK 3.0](https://dotnet.microsoft.com/download)

::: moniker-end

* Klient platformy Docker 18.03 lub nowszy

  * Dystrybucje Linuksa
    * [CentOS](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [Debian](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [macOS](https://docs.docker.com/docker-for-mac/install/)
  * [Windows](https://docs.docker.com/docker-for-windows/install/)

* [Git](https://git-scm.com/download)

## <a name="download-the-sample-app"></a>Pobieranie przykładowej aplikacji

* Pobierz próbkę, klonując [repozytorium platformy .NET Core Docker:](https://github.com/dotnet/dotnet-docker) 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a>Lokalne uruchamianie aplikacji

* Przejdź do folderu projektu w *dotnet-docker/samples/aspnetapp/aspnetapp*.

* Uruchom następujące polecenie, aby skompilować i uruchomić aplikację lokalnie:

  ```dotnetcli
  dotnet run
  ```

* Przejdź `http://localhost:5000` do przeglądarki, aby przetestować aplikację.

* Naciśnij klawisze Ctrl+C w wierszu polecenia, aby zatrzymać aplikację.

## <a name="run-in-a-linux-container"></a>Uruchamianie w kontenerze systemu Linux

* W kliencie platformy Docker przełącz się na kontenery systemu Linux.

* Przejdź do folderu Dockerfile w *dotnet-docker/samples/aspnetapp*.

* Uruchom następujące polecenia, aby skompilować i uruchomić przykład w umiań Docker:

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  Argumenty `build` polecenia:
  * Nazwij obraz aspnetapp.
  * Poszukaj pliku Dockerfile w bieżącym folderze (kropka na końcu).

  Argumenty polecenia uruchom:
  * Przydziel pseudo-TTY i zachowaj go otwartego, nawet jeśli nie jest dołączony. (Taki sam `--interactive --tty`efekt jak .)
  * Automatycznie usuń kontener po jego wyjściu.
  * Mapuj port 5000 na komputerze lokalnym, aby port 80 w kontenerze.
  * Nazwij aspnetcore_sample kontenera.
  * Określ obraz aspnetapp.

* Przejdź `http://localhost:5000` do przeglądarki, aby przetestować aplikację.

## <a name="run-in-a-windows-container"></a>Uruchamianie w kontenerze systemu Windows

* W kliencie platformy Docker przełącz się do kontenerów systemu Windows.

Przejdź do folderu plików `dotnet-docker/samples/aspnetapp`platformy docker w pliku .

* Uruchom następujące polecenia, aby skompilować i uruchomić przykład w umiań Docker:

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* W przypadku kontenerów systemu Windows potrzebny jest adres `http://localhost:5000` IP kontenera (przeglądanie, aby nie działać):
  * Otwórz inny wiersz polecenia.
  * Uruchom, `docker ps` aby wyświetlić uruchomione kontenery. Sprawdź, czy istnieje kontener "aspnetcore_sample".
  * Uruchom, `docker exec aspnetcore_sample ipconfig` aby wyświetlić adres IP kontenera. Dane wyjściowe z polecenia wygląda następująco:

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* Skopiuj adres IPv4 kontenera (na przykład 172.29.245.43) i wklej go na pasku adresu przeglądarki, aby przetestować aplikację.

## <a name="build-and-deploy-manually"></a>Ręczne tworzenie i wdrażanie

W niektórych scenariuszach można wdrożyć aplikację do kontenera, kopiując do niego pliki aplikacji, które są potrzebne w czasie wykonywania. W tej sekcji pokazano, jak wdrożyć ręcznie.

* Przejdź do folderu projektu w *dotnet-docker/samples/aspnetapp/aspnetapp*.

* Uruchom polecenie [publikowania dotnetu:](/dotnet/core/tools/dotnet-publish)

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  Argumenty polecenia:
  * Tworzenie aplikacji w trybie zwalniania (domyślnym jest tryb debugowania).
  * Utwórz pliki w *opublikowanym folderze.*

* Uruchom aplikację.

  * W systemie Windows:

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * W systemie Linux:

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* Przejdź do `http://localhost:5000` strony głównej.

Aby użyć ręcznie opublikowanej aplikacji w kontenerze platformy Docker, utwórz `docker build .` nowy plik Dockerfile i użyj polecenia do utworzenia kontenera.

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>Plik dockerfile

Oto *Dockerfile* używane przez `docker build` polecenie, które uruchomiono wcześniej.  Używa tak `dotnet publish` samo, jak w tej sekcji do tworzenia i wdrażania.  

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

### <a name="the-dockerfile"></a>Plik dockerfile

Oto *Dockerfile* używane przez `docker build` polecenie, które uruchomiono wcześniej.  Używa tak `dotnet publish` samo, jak w tej sekcji do tworzenia i wdrażania.  

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

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Polecenie kompilacji platformy Docker](https://docs.docker.com/engine/reference/commandline/build)
* [Polecenie Uruchom docker](https://docs.docker.com/engine/reference/commandline/run)
* [ASP.NET próbka rdzenia platformy Docker](https://github.com/dotnet/dotnet-docker) (ten używany w tym samouczku).
* [Konfigurowanie ASP.NET Core do pracy z serwerami proxy i modułami równoważenia obciążenia](/aspnet/core/host-and-deploy/proxy-load-balancer)
* [Praca z narzędziami platformy Visual Studio docker](https://docs.microsoft.com/aspnet/core/publishing/visual-studio-tools-for-docker)
* [Debugowanie za pomocą kodu programu Visual Studio](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [GC przy użyciu platformy Docker i małych kontenerów](xref:performance/memory#sc)

## <a name="next-steps"></a>Następne kroki

Repozytorium Git, które zawiera przykładową aplikację, zawiera również dokumentację. Aby uzyskać przegląd zasobów dostępnych w repozytorium, zobacz [plik README](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md). W szczególności dowiedz się, jak zaimplementować protokół HTTPS:

> [!div class="nextstepaction"]
> [Tworzenie ASP.NET podstawowych aplikacji za pomocą platformy Docker za pośrednictwem protokołu HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)
