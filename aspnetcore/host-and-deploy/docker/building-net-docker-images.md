---
title: Obrazy platformy Docker dla ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać opublikowanych obrazów .NET Core Docker z rejestru platformy Docker. Ściągaj obrazy i Kompiluj własne obrazy.
ms.author: riande
ms.custom: mvc
ms.date: 05/12/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 2b77b4fbb960a8d2dbcb67c3f4f32ba9d9459d78
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408087"
---
# <a name="docker-images-for-aspnet-core"></a>Obrazy platformy Docker dla ASP.NET Core

W tym samouczku pokazano, jak uruchomić aplikację ASP.NET Core w kontenerach platformy Docker.

W tym samouczku zostały wykonane następujące czynności:
> [!div class="checklist"]
> * Dowiedz się więcej na temat Microsoft .NET podstawowych obrazów platformy Docker
> * Pobieranie przykładowej aplikacji ASP.NET Core
> * Uruchamianie przykładowej aplikacji lokalnie
> * Uruchamianie przykładowej aplikacji w kontenerach systemu Linux
> * Uruchamianie przykładowej aplikacji w kontenerach systemu Windows
> * Kompiluj i wdrażaj ręcznie

## <a name="aspnet-core-docker-images"></a>ASP.NET Core obrazów platformy Docker

Na potrzeby tego samouczka pobierzesz przykładową aplikację ASP.NET Core i uruchomisz ją w kontenerach platformy Docker. Przykład działa w przypadku kontenerów systemów Linux i Windows.

Przykładowy pliku dockerfile używa [funkcji budowania wielu etapów platformy Docker](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) do kompilowania i uruchamiania w różnych kontenerach. Kontenery kompilacji i uruchamiania są tworzone na podstawie obrazów udostępnianych w usłudze Docker Hub przez firmę Microsoft:

* `dotnet/core/sdk`

  Przykład używa tego obrazu do kompilowania aplikacji. Obraz zawiera zestaw .NET Core SDK, który zawiera narzędzia wiersza polecenia (CLI). Obraz jest zoptymalizowany pod kątem lokalnego projektowania, debugowania i testowania jednostkowego. Narzędzia zainstalowane na potrzeby programowania i kompilowania sprawiają, że jest to stosunkowo duży obraz. 

* `dotnet/core/aspnet`

   Przykład używa tego obrazu do uruchamiania aplikacji. Obraz zawiera ASP.NET Core środowiska uruchomieniowego i bibliotek, które są zoptymalizowane pod kątem uruchamiania aplikacji w środowisku produkcyjnym. Obraz jest stosunkowo mały, zaprojektowany z myślą o szybkości wdrażania i uruchamiania aplikacji, dlatego Optymalizacja wydajności sieci z poziomu rejestru platformy Docker do hosta platformy Docker jest zoptymalizowana. Tylko pliki binarne i zawartość, które są konieczne do uruchomienia aplikacji, są kopiowane do kontenera. Zawartość jest gotowa do uruchomienia, co pozwoli na najszybszy czas od `Docker run` do uruchomienia aplikacji. W modelu platformy Docker nie jest wymagana kompilacja kodu dynamicznego.

## <a name="prerequisites"></a>Wymagania wstępne
::: moniker range="< aspnetcore-3.0"

* [Zestaw SDK platformy .NET Core 2,2](https://dotnet.microsoft.com/download/dotnet-core)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* [Zestaw .NET Core SDK 3,0](https://dotnet.microsoft.com/download)

::: moniker-end

* Klient platformy Docker 18,03 lub nowszy

  * Dystrybucje systemu Linux
    * [CentOS](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [Debian](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [macOS](https://docs.docker.com/docker-for-mac/install/)
  * [Windows](https://docs.docker.com/docker-for-windows/install/)

* [Git](https://git-scm.com/download)

## <a name="download-the-sample-app"></a>Pobieranie przykładowej aplikacji

* Pobierz próbkę, klonowanie [repozytorium .NET Core Docker](https://github.com/dotnet/dotnet-docker): 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a>Lokalne uruchamianie aplikacji

* Przejdź do folderu projektu w programie *dotnet-Docker/Samples/aspnetapp/aspnetapp*.

* Uruchom następujące polecenie, aby skompilować i uruchomić aplikację lokalnie:

  ```dotnetcli
  dotnet run
  ```

* Przejdź do programu `http://localhost:5000` w przeglądarce, aby przetestować aplikację.

* Naciśnij klawisze CTRL + C w wierszu polecenia, aby zatrzymać aplikację.

## <a name="run-in-a-linux-container"></a>Uruchamianie w kontenerze systemu Linux

* W kliencie platformy Docker przejdź do obszaru kontenery systemu Linux.

* Przejdź do folderu pliku dockerfile w programie *dotnet-Docker/Samples/aspnetapp*.

* Uruchom następujące polecenia, aby skompilować i uruchomić przykład w Docker:

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  `build`Argumenty polecenia:
  * Nazwij obraz aspnetapp.
  * Wyszukaj pliku dockerfile w bieżącym folderze (okres na końcu).

  Argumenty polecenia Run:
  * Przydziel pseudo-TTY i pozostaw go otwarty nawet wtedy, gdy nie jest dołączony. (Ten sam efekt jako `--interactive --tty` )
  * Automatycznie Usuń kontener, gdy zostanie on zakończony.
  * Mapuj port 5000 na komputerze lokalnym na port 80 w kontenerze.
  * Nadaj nazwę kontenerowi aspnetcore_sample.
  * Określ obraz aspnetapp.

* Przejdź do programu `http://localhost:5000` w przeglądarce, aby przetestować aplikację.

## <a name="run-in-a-windows-container"></a>Uruchamianie w kontenerze systemu Windows

* W kliencie platformy Docker przejdź do kontenerów systemu Windows.

Przejdź do folderu pliku platformy Docker pod adresem `dotnet-docker/samples/aspnetapp` .

* Uruchom następujące polecenia, aby skompilować i uruchomić przykład w Docker:

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* W przypadku kontenerów systemu Windows wymagany jest adres IP kontenera (przeglądanie w celu `http://localhost:5000` uniemożliwienia pracy):
  * Otwórz inny wiersz polecenia.
  * Uruchom, `docker ps` Aby wyświetlić uruchomione kontenery. Upewnij się, że kontener "aspnetcore_sample" znajduje się w tym miejscu.
  * Uruchom, `docker exec aspnetcore_sample ipconfig` Aby wyświetlić adres IP kontenera. Dane wyjściowe polecenia wyglądają jak w tym przykładzie:

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* Skopiuj adres IPv4 kontenera (na przykład 172.29.245.43) i wklej go na pasku adresu przeglądarki, aby przetestować aplikację.

## <a name="build-and-deploy-manually"></a>Kompiluj i wdrażaj ręcznie

W niektórych scenariuszach może zajść potrzeba wdrożenia aplikacji w kontenerze przez skopiowanie do niego plików aplikacji, które są potrzebne w czasie wykonywania. W tej sekcji pokazano, jak wdrożyć ręcznie.

* Przejdź do folderu projektu w programie *dotnet-Docker/Samples/aspnetapp/aspnetapp*.

* Uruchom [dotnet Publish](/dotnet/core/tools/dotnet-publish) polecenie:

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  Argumenty polecenia:
  * Kompiluj aplikację w trybie wydania (domyślnie jest to tryb debugowania).
  * Utwórz pliki w folderze *opublikowanym* .

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

Aby użyć ręcznie opublikowanej aplikacji w kontenerze platformy Docker, Utwórz nowy pliku dockerfile i Użyj `docker build .` polecenia, aby skompilować kontener.

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>Pliku dockerfile

Oto *pliku dockerfile* używany przez `docker build` wykonane wcześniej polecenie.  Używa tego `dotnet publish` samego sposobu tworzenia i wdrażania w tej sekcji.  

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

### <a name="the-dockerfile"></a>Pliku dockerfile

Oto *pliku dockerfile* używany przez `docker build` wykonane wcześniej polecenie.  Używa tego `dotnet publish` samego sposobu tworzenia i wdrażania w tej sekcji.  

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

Jak wspomniano w poprzednim pliku dockerfile, `*.csproj` pliki są kopiowane i przywracane jako odrębne *warstwy*. Gdy `docker build` polecenie kompiluje obraz, używa wbudowanej pamięci podręcznej. Jeśli `*.csproj` pliki nie uległy zmianie od czasu `docker build` ostatniego uruchomienia polecenia, `dotnet restore` nie trzeba ponownie uruchamiać polecenia. Zamiast tego Wbudowana pamięć podręczna dla odpowiedniej `dotnet restore` warstwy jest używana ponownie. Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dotyczące pisania wieloetapowe dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).

::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Docker Build — polecenie](https://docs.docker.com/engine/reference/commandline/build)
* [Polecenie Docker Run](https://docs.docker.com/engine/reference/commandline/run)
* [ASP.NET Core Docker — przykład](https://github.com/dotnet/dotnet-docker) (używany w tym samouczku).
* [Konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia](/aspnet/core/host-and-deploy/proxy-load-balancer)
* [Praca z narzędziami platformy Docker programu Visual Studio](https://docs.microsoft.com/aspnet/core/publishing/visual-studio-tools-for-docker)
* [Debugowanie za pomocą Visual Studio Code](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [GC przy użyciu platformy Docker i małych kontenerów](xref:performance/memory#sc)

## <a name="next-steps"></a>Następne kroki

Repozytorium git zawierające przykładową aplikację zawiera również dokumentację. Aby zapoznać się z omówieniem zasobów dostępnych w repozytorium, zobacz [plik Readme](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md). W szczególności Dowiedz się, jak zaimplementować protokół HTTPS:

> [!div class="nextstepaction"]
> [Opracowywanie aplikacji ASP.NET Core przy użyciu platformy Docker za pośrednictwem protokołu HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)
