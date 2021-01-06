---
title: Narzędzia kontenerów programu Visual Studio z platformą ASP.NET Core
author: spboyer
description: Dowiedz się, jak używać narzędzi programu Visual Studio i platformy Docker dla systemu Windows w celu konteneryzowania aplikacji ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2018
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
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: 2cfd200c44290a0931cdeb2f68e99b90c11aa612
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059822"
---
# <a name="visual-studio-container-tools-with-aspnet-core"></a>Narzędzia kontenerów programu Visual Studio z platformą ASP.NET Core

Program Visual Studio 2017 i jego nowsze wersje obsługują kompilowanie, debugowanie i uruchamianie konteneryzowanych aplikacji ASP.NET Core przeznaczonych dla platformy .NET Core. Obsługiwane są kontenery zarówno systemu Windows, jak i Linux.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

* [Docker for Windows](https://docs.docker.com/docker-for-windows/install/)
* [Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z obciążeniem **Programowanie dla wielu platform w środowisku .NET Core**

## <a name="installation-and-setup"></a>Instalacja i konfiguracja

W przypadku instalacji platformy Docker zapoznaj się z informacjami w [Docker for Windows: co należy wiedzieć przed zainstalowaniem](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)programu. Następnie zainstaluj [platformę Docker dla systemu Windows](https://docs.docker.com/docker-for-windows/install/).

**[Udostępnione dyski](https://docs.docker.com/docker-for-windows/#shared-drives)** na platformie Docker dla systemu Windows muszą być skonfigurowane do obsługi mapowania woluminów i debugowania. Kliknij prawym przyciskiem myszy ikonę platformy Docker na pasku zadań, wybierz pozycję **Settings** (Ustawienia) i wybierz pozycję **Shared Drives** (Udostępnione dyski). Wybierz dysk, na którym platforma Docker przechowuje pliki. Kliknij pozycję **Zastosuj**.

![Okno dialogowe umożliwiające wybranie lokalnego udostępnionego dysku C na potrzeby kontenerów](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> W programie Visual Studio 2017 w wersji 15.6 i nowszych jest wyświetlany monit, jeśli **udostępnione dyski** nie są skonfigurowane.

## <a name="add-a-project-to-a-docker-container"></a>Dodawanie projektu do kontenera platformy Docker

Aby umieścić w kontenerze projekt ASP.NET Core, musi on być przeznaczony dla platformy .NET Core. Obsługiwane są kontenery zarówno systemu Linux, jak i Windows.

Podczas dodawania obsługi platformy Docker do projektu wybierz kontener systemu Windows lub Linux. Na hoście platformy Docker musi być uruchomiony ten sam typ kontenera. Aby zmienić typ kontenera w uruchomionym wystąpieniu platformy Docker, kliknij prawym przyciskiem myszy ikonę platformy Docker na pasku zadań i wybierz polecenie **Switch to Windows containers...** (Przełącz do kontenerów systemu Windows...) lub polecenie **Switch to Linux containers...** (Przełącz do kontenerów systemu Linux...).

### <a name="new-app"></a>Nowa aplikacja

Podczas tworzenia nowej aplikacji przy użyciu szablonów projektu **Aplikacja internetowa platformy ASP.NET Core** zaznacz pole wyboru **Włącz obsługę platformy Docker**:

![Pole wyboru Włącz obsługę platformy Docker](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

Jeśli platformą docelową jest .NET Core, lista rozwijana **System operacyjny** umożliwia wybranie typu kontenera.

### <a name="existing-app"></a>Istniejąca aplikacja

W przypadku projektów ASP.NET Core przeznaczonych dla platformy .NET Core dostępne są dwie opcje dodawania obsługi platformy Docker za pomocą narzędzi. Otwórz projekt w programie Visual Studio, a następnie wybierz jedną z następujących opcji:

* Wybierz pozycję **Obsługa platformy Docker** z menu **Projekt**.
* Kliknij projekt prawym przyciskiem myszy w **Eksploratorze rozwiązań** i wybierz pozycję **Dodaj** > **Obsługa platformy Docker**.

Narzędzia kontenerów programu Visual Studio nie obsługują dodawania platformy Docker do istniejącego projektu ASP.NET Core przeznaczonego dla platformy .NET Framework.

## <a name="dockerfile-overview"></a>Plik Dockerfile — przegląd

Plik *Dockerfile*, zawierający opis tworzenia końcowego obrazu platformy Docker, jest dodawany do katalogu głównego projektu. Opis poleceń znajdujących się w tym pliku można znaleźć w [dokumentacji pliku Dockerfile](https://docs.docker.com/engine/reference/builder/). Ten konkretny plik *Dockerfile* używa [kompilacji wieloetapowej](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) z czterema odrębnymi, nazwanymi etapami kompilacji:

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

Wcześniejszy plik *Dockerfile* bazuje na obrazie [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/). Ten obraz podstawowy obejmuje środowisko uruchomieniowe ASP.NET Core i pakiety NuGet. Pakiety są kompilowane w trybie Just-In-Time (JIT), aby zwiększyć wydajność uruchamiania.

Jeśli w oknie dialogowym nowego projektu zostanie zaznaczone pole wyboru **Konfiguruj dla protokołu HTTPS**, plik *Dockerfile* uwidacznia dwa porty. Jeden port jest używany na potrzeby ruchu HTTP, a drugi na potrzeby protokołu HTTPS. Jeśli to pole wyboru nie zostanie zaznaczone, dla ruchu HTTP zostanie uwidoczniony pojedynczy port (80).

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

Wcześniejszy plik *Dockerfile* bazuje na obrazie [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/). Ten obraz podstawowy zawiera pakiety NuGet ASP.NET Core, które są kompilowane w trybie Just-In-Time (JIT), aby zwiększyć wydajność uruchamiania.

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a>Dodawanie do aplikacji obsługi orkiestratora kontenerów

Program Visual Studio 2017 w wersji 15.7 lub starszych jako rozwiązanie aranżacji kontenerów obsługuje tylko narzędzie [Docker Compose](https://docs.docker.com/compose/overview/). Docker Compose artefakty są dodawane za pośrednictwem **dodawania**  >  **obsługi platformy Docker**.

W programie Visual Studio 2017 w wersji 15.8 i nowszych rozwiązanie aranżacji jest dodawane tylko na wyraźne żądanie. Kliknij projekt prawym przyciskiem myszy w **Eksploratorze rozwiązań** i wybierz pozycję **Dodaj** > **Obsługa orkiestratora kontenerów**. Dostępne są następujące opcje: 

* [Docker Compose](#docker-compose)
* [Service Fabric](#service-fabric)
* [Kubernetes/Helm ](https://helm.sh/)

### <a name="docker-compose"></a>Docker Compose

Narzędzia kontenerów programu Visual Studio dodają do rozwiązania projekt *docker-compose* z następującymi plikami:

* *Docker-Zredaguj. dcproj*: plik reprezentujący projekt. Zawiera element `<DockerTargetOS>` określający system operacyjny, który ma być używany.
* *. dockerignore*: wyświetla wzorce plików i katalogów, które mają zostać wykluczone podczas generowania kontekstu kompilacji.
* *Docker-Compose. yml*: podstawowy plik [Docker Compose](https://docs.docker.com/compose/overview/) używany do definiowania kolekcji obrazów skompilowanych i uruchamianych z `docker-compose build` i `docker-compose run` , odpowiednio.
* *Docker-Compose. override. yml*: opcjonalny plik, Odczytaj według Docker Compose, z zastąpieniami konfiguracji dla usług. Program Visual Studio wykonuje polecenie `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"`, aby scalić te pliki.

Plik *docker-compose.yml* przywołuje nazwę obrazu utworzonego podczas uruchamiania projektu:

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

W poprzednim przykładzie polecenie `image: hellodockertools` generuje obraz `hellodockertools:dev`, gdy aplikacja działa w trybie **debugowania**. Obraz `hellodockertools:latest` jest generowany, gdy aplikacja działa w trybie **wydania**.

Dodaj do nazwy obrazu prefiks [Docker Hub](https://hub.docker.com/) nazwa użytkownika (na przykład `dockerhubusername/hellodockertools`), jeśli obraz jest wypychany do rejestru. Możesz również zmienić nazwę obrazu tak, aby zawierała adres URL rejestru prywatnego (na przykład `privateregistry.domain.com/hellodockertools`) w zależności od konfiguracji.

Jeśli chcesz wymusić różne zachowania w zależności od konfiguracji kompilacji (na przykład debugowanie lub wydanie), dodaj specyficzne dla konfiguracji pliki *docker-compose*. Pliki powinny mieć nazwy zgodne z konfiguracją kompilacji (na przykład *docker-compose.vs.debug.yml* i *docker-compose.vs.release.yml*) i powinny być umieszczone w tej samej lokalizacji co plik *docker-compose-override.yml*. 

Przy użyciu plików przesłonięć specyficznych dla konfiguracji można określić różne ustawienia konfiguracji (na przykład zmienne środowiskowe lub punkty wejścia) dla kompilacji w trybie debugowania i wydania.

Aby w narzędziu Docker Compose była wyświetlana opcja uruchamiania w programie Visual Studio, projekt platformy Docker musi być projektem startowym.

### <a name="service-fabric"></a>Service Fabric

Oprócz podstawowych [wymagań wstępnych](#prerequisites) rozwiązanie aranżacji [Service Fabric](/azure/service-fabric/) ma następujące wymagania wstępne:

* Pakiet [Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) w wersji 2.6 lub nowszej
* Obciążenie **programowania platformy Azure** dla programu Visual Studio

Usługa Service Fabric nie obsługuje uruchamiania kontenerów systemu Linux w lokalnym klastrze programistycznym w systemie Windows. Jeśli projekt używa już kontenera systemu Linux, program Visual Studio wyświetli komunikat z prośbą o przełączenie do kontenerów systemu Windows.

Narzędzia kontenerów programu Visual Studio wykonują następujące zadania:

* Dodaje *&lt; project_name &gt; aplikacji* **Service Fabric projektu aplikacji** do rozwiązania.
* Dodają do projektu ASP.NET Core pliki *Dockerfile* i *.dockerignore*. Jeśli plik *Dockerfile* już istnieje w projekcie ASP.NET Core, jego nazwa zostanie zmieniona na *Dockerfile.original*. Tworzony jest nowy plik *Dockerfile* podobny do następującego:

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* Dodają element `<IsServiceFabricServiceProject>` do pliku *.csproj* projektu ASP.NET Core:

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* Dodają folder *PackageRoot* do projektu ASP.NET Core. Ten folder zawiera manifest usługi i ustawienia nowej usługi.

Aby uzyskać więcej informacji, zobacz [Wdrażanie aplikacji .NET w kontenerze systemu Windows w usłudze Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).

## <a name="debug"></a>Debugowanie

Wybierz pozycję **Docker** z listy rozwijanej debugowania na pasku narzędzi i rozpocznij debugowanie aplikacji. W widoku **Docker** w oknie **Dane wyjściowe** widać wykonywanie następujących akcji:

::: moniker range=">= aspnetcore-2.1"

* Uzyskiwany jest tag *2.1-aspnetcore-runtime* obrazu środowiska uruchomieniowego *microsoft/dotnet* (jeśli nie znajduje się już w pamięci podręcznej). Obraz instaluje środowiska uruchomieniowe platform ASP.NET Core i .NET Core oraz powiązane biblioteki. Są one zoptymalizowane pod kątem uruchamiania aplikacji ASP.NET Core w środowisku produkcyjnym.
* Zmienna środowiskowa `ASPNETCORE_ENVIRONMENT` jest ustawiona na wartość `Development` w kontenerze.
* Uwidaczniane są dwa dynamicznie przypisywane porty: jeden dla protokołu HTTP i jeden dla protokołu HTTPS. Do portu przypisanego do hosta lokalnego można wysłać polecenie `docker ps`.
* Aplikacja jest kopiowana do kontenera.
* Uruchamiana jest domyślna przeglądarka z debugerem dołączonym do kontenera przy użyciu dynamicznie przypisanego portu.

Wynikowy obraz platformy Docker aplikacji jest oznaczony tagiem *dev*. Obraz bazuje na tagu *2.1-aspnetcore-runtime* obrazu podstawowego *microsoft/dotnet*. Uruchom polecenie `docker images` w oknie **konsoli menedżera pakietów** (PMC). Na komputerze są wyświetlane następujące obrazy:

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* Uzyskiwany jest obraz środowiska uruchomieniowego *microsoft/aspnetcore* (jeśli nie znajduje się już w pamięci podręcznej).
* Zmienna środowiskowa `ASPNETCORE_ENVIRONMENT` jest ustawiona na wartość `Development` w kontenerze.
* Port 80 jest uwidaczniany i mapowany na dynamicznie przypisany port dla hosta lokalnego. Port jest określany przez hosta platformy Docker i można do niego wysłać zapytanie przy użyciu polecenia `docker ps`.
* Aplikacja jest kopiowana do kontenera.
* Uruchamiana jest domyślna przeglądarka z debugerem dołączonym do kontenera przy użyciu dynamicznie przypisanego portu.

Wynikowy obraz platformy Docker aplikacji jest oznaczony tagiem *dev*. Obraz bazuje na obrazie podstawowym *microsoft/aspnetcore*. Uruchom polecenie `docker images` w oknie **konsoli menedżera pakietów** (PMC). Na komputerze są wyświetlane następujące obrazy:

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> Obraz *dev* nie zawiera zawartości aplikacji, ponieważ konfiguracje **debugowania** używają instalacji woluminu w celu zapewnienia iteracyjnych działań. Aby wypchnąć obraz, należy użyć konfiguracji **wydania**.

Uruchom polecenie `docker ps` w konsoli PMC. Zauważ, że aplikacja działa przy użyciu kontenera:

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a>Edytowanie i kontynuowanie

Zmiany w plikach i Razor widokach statycznych są automatycznie aktualizowane bez konieczności wykonywania kroku kompilacji. Wprowadź zmiany, zapisz je i odśwież przeglądarkę, aby wyświetlić aktualizację.

Modyfikacje pliku kodu wymagają kompilacji i ponownego uruchomienia serwera Kestrel w kontenerze. Po wprowadzeniu zmiany użyj klawiszy `CTRL+F5`, aby wykonać przetwarzanie i uruchomić aplikację w kontenerze. Kontener platformy Docker nie jest ponownie kompilowany ani zatrzymywany. Uruchom polecenie `docker ps` w konsoli PMC. Zwróć uwagę, że oryginalny kontener nadal działa od 10 minut:

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a>Publikowanie obrazów platformy Docker

Po zakończeniu cyklu opracowywania i debugowania aplikacji narzędzia kontenerów programu Visual Studio ułatwiają tworzenie obrazu produkcyjnego aplikacji. Zmień opcję listy rozwijanej konfiguracji na **Wydanie** i skompiluj aplikację. Narzędzia uzyskują obraz kompilowania/publikowania z usługi Docker Hub (jeśli jeszcze nie znajduje się on w pamięci podręcznej). Obraz jest tworzony z tagiem *latest* (najnowszy) i można go wypchnąć do rejestru prywatnego lub usługi Docker Hub.

Uruchom polecenie `docker images` w konsoli PMC, aby wyświetlić listę obrazów. Wyświetlane są dane wyjściowe podobne do następujących:

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

Obrazy `microsoft/aspnetcore-build` i `microsoft/aspnetcore` wyświetlane w poprzednich danych wyjściowych zostały zastąpione obrazami `microsoft/dotnet` platformy .NET Core 2.1. Aby uzyskać więcej informacji, zobacz [zapowiedź migracji repozytoriów platformy Docker](https://github.com/aspnet/Announcements/issues/298).

::: moniker-end

> [!NOTE]
> `docker images`Polecenie zwraca obrazy pośredniczące z nazwami repozytoriów i tagami identyfikowanych jako *\<none>* (niewymienione powyżej). Te obrazy bez nazwy są tworzone przez [Wieloetapową kompilację](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *pliku dockerfile*. Poprawiają one wydajność kompilowania końcowego obrazu &mdash; w przypadku wystąpienia zmian tylko niezbędne warstwy są ponownie kompilowane. Gdy obrazy pośrednie nie będą już potrzebne, można je usunąć za pomocą polecenia [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/).

Może wystąpić oczekiwanie, że rozmiar obrazu produkcyjnego lub wydania będzie mniejszy niż obraz *dev*. Ze względu na mapowanie woluminu debuger i aplikacja były uruchomione z poziomu komputera lokalnego, a nie w kontenerze. Obraz *latest* (najnowszy) zawiera spakowany kod aplikacji niezbędny do uruchomienia aplikacji na komputerze hosta. Różnica w rozmiarach wynika zatem z rozmiaru kodu aplikacji.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Opracowywanie kontenerów w programie Visual Studio](/visualstudio/containers)
* [Azure Service Fabric: Przygotowywanie środowiska deweloperskiego](/azure/service-fabric/service-fabric-get-started)
* [Wdrażanie aplikacji .NET w kontenerze systemu Windows w usłudze Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container)
* [Rozwiązywanie problemów związanych z opracowywaniem zwartości w programie Visual Studio przy użyciu platformy Docker](/azure/vs-azure-tools-docker-troubleshooting-docker-errors)
* [Repozytorium usługi GitHub dla narzędzi kontenerów programu Visual Studio](https://github.com/Microsoft/DockerTools)
* [GC przy użyciu platformy Docker i małych kontenerów](xref:performance/memory#sc)
