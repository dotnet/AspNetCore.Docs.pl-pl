---
title: Hostuj i wdrażaj ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak skonfigurować środowiska hostingowe i wdrożyć ASP.NET aplikacje Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/index
ms.openlocfilehash: 464d19bd63e1f0f06bd7d218e7644afde04a5672
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657921"
---
# <a name="host-and-deploy-aspnet-core"></a>Hostuj i wdrażaj ASP.NET Core

::: moniker range=">= aspnetcore-2.2"

Ogólnie rzecz biorąc, aby wdrożyć aplikację core ASP.NET w środowisku hostingowym:

* Wdrażanie opublikowanej aplikacji w folderze na serwerze hostingowym.
* Skonfiguruj menedżera procesów, który uruchamia aplikację po odebraniu żądań i uruchamia ponownie aplikację po awarii lub ponownym uruchomieniu serwera.
* W przypadku konfiguracji odwrotnego serwera proxy należy skonfigurować odwrotny serwer proxy do przesyłania dalej żądań do aplikacji.

## <a name="publish-to-a-folder"></a>Publikowanie w folderze

Polecenie [publikowania dotnet](/dotnet/core/tools/dotnet-publish) kompiluje kod aplikacji i kopiuje pliki wymagane do uruchomienia aplikacji do folderu *publikowania.* Podczas wdrażania z programu `dotnet publish` Visual Studio, krok występuje automatycznie przed pliki są kopiowane do miejsca docelowego wdrożenia.

### <a name="folder-contents"></a>Zawartość folderu

Folder *publikowania* zawiera co najmniej jeden plik zestawu aplikacji, zależności i opcjonalnie środowisko uruchomieniowe .NET.

Aplikacja .NET Core może zostać opublikowana jako *samodzielne wdrożenie* lub *wdrożenie zależne od struktury.* Jeśli aplikacja jest niezależna, pliki zestawu, które zawierają środowisko uruchomieniowe .NET są zawarte w folderze *publikowania.* Jeśli aplikacja jest zależna od struktury, pliki środowiska uruchomieniowego platformy .NET nie są uwzględniane, ponieważ aplikacja ma odwołanie do wersji platformy .NET zainstalowanej na serwerze. Domyślny model wdrażania jest zależny od struktury. Aby uzyskać więcej informacji, zobacz [.NET Core wdrożenia aplikacji](/dotnet/core/deploying/).

Oprócz plików *.exe* i *.dll* folder *publikowania* aplikacji ASP.NET Core zazwyczaj zawiera pliki konfiguracyjne, zasoby statyczne i widoki MVC. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/directory-structure>.

## <a name="set-up-a-process-manager"></a>Konfigurowanie menedżera procesów

Aplikacja ASP.NET Core to aplikacja konsoli, która musi zostać uruchomiona po uruchomieniu serwera i ponownym uruchomieniu w przypadku awarii. Aby zautomatyzować uruchamianie i ponowne uruchamianie, wymagany jest menedżer procesów. Najczęstszymi menedżerami procesów dla ASP.NET Core są:

* Linux
  * [Nginx](xref:host-and-deploy/linux-nginx)
  * [Apache](xref:host-and-deploy/linux-apache)
* Windows
  * [IIS](xref:host-and-deploy/iis/index)
  * [Usługa systemu Windows](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>Konfigurowanie odwrotnego serwera proxy

Jeśli aplikacja korzysta z serwera [Kestrel,](xref:fundamentals/servers/kestrel) [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache)lub [IIS](xref:host-and-deploy/iis/index) może być używany jako serwer odwrotnego serwera proxy. Serwer odwrotnego serwera proxy odbiera żądania HTTP z Internetu i przekazuje je do Kestrel.

Konfiguracja&mdash;z odwrotnym serwerem&mdash;proxy lub bez niego jest obsługiwaną konfiguracją hostingu. Aby uzyskać więcej informacji, zobacz [Kiedy używać Kestrel z odwrotnym serwerem proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scenariusze serwera proxy i modułu równoważenia obciążenia

Dodatkowa konfiguracja może być wymagana dla aplikacji hostowanych za serwerami proxy i modułami równoważenia obciążenia. Bez dodatkowej konfiguracji aplikacja może nie mieć dostępu do schematu (HTTP/HTTPS) i zdalnego adresu IP, z którego pochodzi żądanie. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ASP.NET Core do pracy z serwerami proxy i modułami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Automatyzacja wdrożeń za pomocą programu Visual Studio i MSBuild

Wdrożenie często wymaga dodatkowych zadań oprócz kopiowania danych wyjściowych z [dotnet publikowania](/dotnet/core/tools/dotnet-publish) na serwerze. Na przykład dodatkowe pliki mogą być wymagane lub wykluczone z folderu *publikowania.* Visual Studio używa MSBuild do wdrożenia sieci web i MSBuild można dostosować do wykonywania wielu innych zadań podczas wdrażania. Aby uzyskać więcej <xref:host-and-deploy/visual-studio-publish-profiles> informacji, zobacz i [przy użyciu MSBuild i Team Foundation Build](http://msbuildbook.com/) książki.

Za pomocą [funkcji publikowania sieci Web](xref:tutorials/publish-to-azure-webapp-using-vs) lub [wbudowanej obsługi git,](xref:host-and-deploy/azure-apps/azure-continuous-deployment)aplikacje można wdrożyć bezpośrednio z programu Visual Studio do usługi Azure App Service. Usługi Azure DevOps obsługują [ciągłe wdrażanie usługi Azure App Service.](/azure/devops/pipelines/targets/webapp) Aby uzyskać więcej informacji, zobacz [DevOps z ASP.NET Core i Azure](xref:azure/devops/index).

## <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Zobacz <xref:tutorials/publish-to-azure-webapp-using-vs> instrukcje dotyczące publikowania aplikacji na platformie Azure przy użyciu programu Visual Studio. Dodatkowy przykład jest dostarczany przez [Tworzenie ASP.NET podstawowej aplikacji sieci web na platformie Azure](/azure/app-service/app-service-web-get-started-dotnet).

## <a name="publish-with-msdeploy-on-windows"></a>Publikowanie w systemie MSDeploy w systemie Windows

Zobacz <xref:host-and-deploy/visual-studio-publish-profiles> instrukcje dotyczące publikowania aplikacji z profilem publikowania programu Visual Studio, w tym z wiersza polecenia systemu Windows przy użyciu polecenia [dotnet msbuild.](/dotnet/core/tools/dotnet-msbuild)

## <a name="internet-information-services-iis"></a>Internet Information Services (IIS)

Aby zapoznać się z wdrożeniami w internetowych usługach informacyjnych (IIS) <xref:host-and-deploy/iis/index>z konfiguracją dostarczoną przez plik *web.config,* zobacz artykuły w obszarze .

## <a name="host-in-a-web-farm"></a>Hosting w farmie internetowej

Aby uzyskać informacje na temat konfiguracji hostingu ASP.NET aplikacje Core w środowisku farmy internetowej (na <xref:host-and-deploy/web-farm>przykład wdrażanie wielu wystąpień aplikacji w celu skalowalności), zobacz .

## <a name="host-on-docker"></a>Host w programie Docker

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/docker/index>.

## <a name="perform-health-checks"></a>Przeprowadzanie kontroli kondycji

Użyj oprogramowania pośredniczącego sprawdzania kondycji, aby przeprowadzić kontrole kondycji aplikacji i jej zależności. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/health-checks>.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:test/troubleshoot>
* [ASP.NET Hosting](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Ogólnie rzecz biorąc, aby wdrożyć aplikację core ASP.NET w środowisku hostingowym:

* Wdrażanie opublikowanej aplikacji w folderze na serwerze hostingowym.
* Skonfiguruj menedżera procesów, który uruchamia aplikację po odebraniu żądań i uruchamia ponownie aplikację po awarii lub ponownym uruchomieniu serwera.
* W przypadku konfiguracji odwrotnego serwera proxy należy skonfigurować odwrotny serwer proxy do przesyłania dalej żądań do aplikacji.

## <a name="publish-to-a-folder"></a>Publikowanie w folderze

Polecenie [publikowania dotnet](/dotnet/core/tools/dotnet-publish) kompiluje kod aplikacji i kopiuje pliki wymagane do uruchomienia aplikacji do folderu *publikowania.* Podczas wdrażania z programu `dotnet publish` Visual Studio, krok występuje automatycznie przed pliki są kopiowane do miejsca docelowego wdrożenia.

### <a name="folder-contents"></a>Zawartość folderu

Folder *publikowania* zawiera co najmniej jeden plik zestawu aplikacji, zależności i opcjonalnie środowisko uruchomieniowe .NET.

Aplikacja .NET Core może zostać opublikowana jako *samodzielne wdrożenie* lub *wdrożenie zależne od struktury.* Jeśli aplikacja jest niezależna, pliki zestawu, które zawierają środowisko uruchomieniowe .NET są zawarte w folderze *publikowania.* Jeśli aplikacja jest zależna od struktury, pliki środowiska uruchomieniowego platformy .NET nie są uwzględniane, ponieważ aplikacja ma odwołanie do wersji platformy .NET zainstalowanej na serwerze. Domyślny model wdrażania jest zależny od struktury. Aby uzyskać więcej informacji, zobacz [.NET Core wdrożenia aplikacji](/dotnet/core/deploying/).

Oprócz plików *.exe* i *.dll* folder *publikowania* aplikacji ASP.NET Core zazwyczaj zawiera pliki konfiguracyjne, zasoby statyczne i widoki MVC. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/directory-structure>.

## <a name="set-up-a-process-manager"></a>Konfigurowanie menedżera procesów

Aplikacja ASP.NET Core to aplikacja konsoli, która musi zostać uruchomiona po uruchomieniu serwera i ponownym uruchomieniu w przypadku awarii. Aby zautomatyzować uruchamianie i ponowne uruchamianie, wymagany jest menedżer procesów. Najczęstszymi menedżerami procesów dla ASP.NET Core są:

* Linux
  * [Nginx](xref:host-and-deploy/linux-nginx)
  * [Apache](xref:host-and-deploy/linux-apache)
* Windows
  * [IIS](xref:host-and-deploy/iis/index)
  * [Usługa systemu Windows](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>Konfigurowanie odwrotnego serwera proxy

Jeśli aplikacja korzysta z serwera [Kestrel,](xref:fundamentals/servers/kestrel) [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache)lub [IIS](xref:host-and-deploy/iis/index) może być używany jako serwer odwrotnego serwera proxy. Serwer odwrotnego serwera proxy odbiera żądania HTTP z Internetu i przekazuje je do Kestrel.

Konfiguracja&mdash;z odwrotnym serwerem&mdash;proxy lub bez niego jest obsługiwaną konfiguracją hostingu. Aby uzyskać więcej informacji, zobacz [Kiedy używać Kestrel z odwrotnym serwerem proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scenariusze serwera proxy i modułu równoważenia obciążenia

Dodatkowa konfiguracja może być wymagana dla aplikacji hostowanych za serwerami proxy i modułami równoważenia obciążenia. Bez dodatkowej konfiguracji aplikacja może nie mieć dostępu do schematu (HTTP/HTTPS) i zdalnego adresu IP, z którego pochodzi żądanie. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ASP.NET Core do pracy z serwerami proxy i modułami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Automatyzacja wdrożeń za pomocą programu Visual Studio i MSBuild

Wdrożenie często wymaga dodatkowych zadań oprócz kopiowania danych wyjściowych z [dotnet publikowania](/dotnet/core/tools/dotnet-publish) na serwerze. Na przykład dodatkowe pliki mogą być wymagane lub wykluczone z folderu *publikowania.* Visual Studio używa MSBuild do wdrożenia sieci web i MSBuild można dostosować do wykonywania wielu innych zadań podczas wdrażania. Aby uzyskać więcej <xref:host-and-deploy/visual-studio-publish-profiles> informacji, zobacz i [przy użyciu MSBuild i Team Foundation Build](http://msbuildbook.com/) książki.

Za pomocą [funkcji publikowania sieci Web](xref:tutorials/publish-to-azure-webapp-using-vs) lub [wbudowanej obsługi git,](xref:host-and-deploy/azure-apps/azure-continuous-deployment)aplikacje można wdrożyć bezpośrednio z programu Visual Studio do usługi Azure App Service. Usługi Azure DevOps obsługują [ciągłe wdrażanie usługi Azure App Service.](/azure/devops/pipelines/targets/webapp) Aby uzyskać więcej informacji, zobacz [DevOps z ASP.NET Core i Azure](xref:azure/devops/index).

## <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Zobacz <xref:tutorials/publish-to-azure-webapp-using-vs> instrukcje dotyczące publikowania aplikacji na platformie Azure przy użyciu programu Visual Studio. Dodatkowy przykład jest dostarczany przez [Tworzenie ASP.NET podstawowej aplikacji sieci web na platformie Azure](/azure/app-service/app-service-web-get-started-dotnet).

## <a name="publish-with-msdeploy-on-windows"></a>Publikowanie w systemie MSDeploy w systemie Windows

Zobacz <xref:host-and-deploy/visual-studio-publish-profiles> instrukcje dotyczące publikowania aplikacji z profilem publikowania programu Visual Studio, w tym z wiersza polecenia systemu Windows przy użyciu polecenia [dotnet msbuild.](/dotnet/core/tools/dotnet-msbuild)

## <a name="internet-information-services-iis"></a>Internet Information Services (IIS)

Aby zapoznać się z wdrożeniami w internetowych usługach informacyjnych (IIS) <xref:host-and-deploy/iis/index>z konfiguracją dostarczoną przez plik *web.config,* zobacz artykuły w obszarze .

## <a name="host-in-a-web-farm"></a>Hosting w farmie internetowej

Aby uzyskać informacje na temat konfiguracji hostingu ASP.NET aplikacje Core w środowisku farmy internetowej (na <xref:host-and-deploy/web-farm>przykład wdrażanie wielu wystąpień aplikacji w celu skalowalności), zobacz .

## <a name="host-on-docker"></a>Host w programie Docker

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/docker/index>.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:test/troubleshoot>
* [ASP.NET Hosting](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
