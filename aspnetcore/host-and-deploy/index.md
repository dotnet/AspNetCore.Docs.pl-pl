---
title: Hostowanie i wdrażanie ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak konfigurować środowiska hostingu i wdrażać ASP.NET Core aplikacje.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
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
ms.openlocfilehash: 0111b7944bc95932937f0fd74524e3960c5fb681
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634621"
---
# <a name="host-and-deploy-aspnet-core"></a>Hostowanie i wdrażanie ASP.NET Core

::: moniker range=">= aspnetcore-2.2"

Ogólnie rzecz biorąc, aby wdrożyć aplikację ASP.NET Core w środowisku hostingu:

* Wdróż opublikowaną aplikację w folderze na serwerze hostingu.
* Skonfiguruj Menedżera procesów uruchamiający aplikację po nadejściu żądań i ponownym uruchomieniu aplikacji po awarii lub ponownym uruchomieniu serwera.
* W przypadku konfiguracji zwrotnego serwera proxy Skonfiguruj zwrotny serwer proxy do przesyłania dalej żądań do aplikacji.

## <a name="publish-to-a-folder"></a>Publikowanie w folderze

[Dotnet Publish](/dotnet/core/tools/dotnet-publish) polecenie kompiluje kod aplikacji i kopiuje pliki wymagane do uruchomienia aplikacji w folderze *publikacji* . Podczas wdrażania z programu Visual Studio, ten `dotnet publish` krok odbywa się automatycznie przed skopiowaniem plików do miejsca docelowego wdrożenia.

### <a name="folder-contents"></a>Zawartość folderu

Folder *publikowania* zawiera jeden lub więcej plików zestawów aplikacji, zależności i opcjonalnie środowiska uruchomieniowego platformy .NET.

Aplikację platformy .NET Core można opublikować jako *samodzielne wdrożenie* lub *wdrożenie zależne od platformy*. Jeśli aplikacja jest samodzielna, pliki zestawu, które zawierają środowisko uruchomieniowe platformy .NET, znajdują się w folderze *Publikowanie* . Jeśli aplikacja jest zależna od struktury, pliki środowiska uruchomieniowego platformy .NET nie są uwzględniane, ponieważ aplikacja ma odwołanie do wersji platformy .NET zainstalowanej na serwerze. Domyślny model wdrażania jest zależny od platformy. Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji .NET Core](/dotnet/core/deploying/).

Oprócz plików *exe* i *dll* , folder *publikacji* aplikacji ASP.NET Core zwykle zawiera pliki konfiguracji, zasoby statyczne i widoki MVC. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/directory-structure>.

## <a name="set-up-a-process-manager"></a>Konfigurowanie Menedżera procesów

Aplikacja ASP.NET Core jest aplikacją konsolową, która musi zostać uruchomiona w przypadku awarii i ponownego uruchomienia serwera. Aby zautomatyzować uruchamianie i ponowne uruchamianie, wymagany jest Menedżer procesów. Najbardziej typowymi menedżerami procesów dla ASP.NET Core są:

* Linux
  * [Nginx](xref:host-and-deploy/linux-nginx)
  * [Apache](xref:host-and-deploy/linux-apache)
* Windows
  * [IIS](xref:host-and-deploy/iis/index)
  * [Usługa systemu Windows](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>Konfigurowanie zwrotnego serwera proxy

Jeśli aplikacja używa serwera [Kestrel](xref:fundamentals/servers/kestrel) , usługi [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache)lub [IIS](xref:host-and-deploy/iis/index) mogą być używane jako zwrotny serwer proxy. Odwrotny serwer proxy odbiera żądania HTTP z Internetu i przekazuje je do usługi Kestrel.

Jest to &mdash; obsługiwana konfiguracja hostingu, która jest konfiguracją z serwerem odwrotnego serwera proxy lub bez niego &mdash; . Aby uzyskać więcej informacji, zobacz [Kiedy używać Kestrel z zwrotnym serwerem proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scenariusze serwera proxy i modułu równoważenia obciążenia

Dodatkowa konfiguracja może być wymagana dla aplikacji hostowanych w ramach serwerów proxy i modułów równoważenia obciążenia. Bez dodatkowej konfiguracji aplikacja może nie mieć dostępu do schematu (HTTP/HTTPS) i zdalnego adresu IP, z którego pochodzi żądanie. Aby uzyskać więcej informacji, zobacz [konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Korzystanie z programu Visual Studio i narzędzia MSBuild do automatyzowania wdrożeń

Wdrożenie często wymaga dodatkowych zadań oprócz kopiowania danych wyjściowych z [dotnet Publish](/dotnet/core/tools/dotnet-publish) na serwer. Na przykład dodatkowe pliki mogą być wymagane lub wykluczone z folderu *publikacji* . Program Visual Studio używa programu MSBuild do wdrażania w sieci Web, a programu MSBuild można dostosować do wykonywania wielu innych zadań podczas wdrażania. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/visual-studio-publish-profiles> i książka [using programu MSBuild i Team Foundation Build](http://msbuildbook.com/) .

Za pomocą [funkcji publikowania w sieci Web](xref:tutorials/publish-to-azure-webapp-using-vs) lub [wbudowanej obsługi narzędzia Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment)aplikacje można wdrażać bezpośrednio z programu Visual Studio do Azure App Service. Azure DevOps Services obsługuje [ciągłe wdrażanie do Azure App Service](/azure/devops/pipelines/targets/webapp). Aby uzyskać więcej informacji, zobacz [DevOps with ASP.NET Core i platformę Azure](xref:azure/devops/index).

## <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Zobacz, <xref:tutorials/publish-to-azure-webapp-using-vs> Aby uzyskać instrukcje dotyczące publikowania aplikacji na platformie Azure przy użyciu programu Visual Studio. Dodatkowy przykład jest zapewniany przez [utworzenie aplikacji sieci web ASP.NET Core na platformie Azure](/azure/app-service/app-service-web-get-started-dotnet).

## <a name="publish-with-msdeploy-on-windows"></a>Publikowanie z MSDeploy w systemie Windows

Zobacz <xref:host-and-deploy/visual-studio-publish-profiles> , aby uzyskać instrukcje dotyczące publikowania aplikacji przy użyciu profilu publikacji programu Visual Studio, w tym z wiersza polecenia systemu Windows przy użyciu polecenia [MSBuild programu dotnet](/dotnet/core/tools/dotnet-msbuild) .

## <a name="internet-information-services-iis"></a>Internet Information Services (IIS)

W przypadku wdrożeń do Internet Information Services (IIS) z konfiguracją dostarczoną przez plik *web.config* zapoznaj się z artykułami poniżej <xref:host-and-deploy/iis/index> .

## <a name="host-in-a-web-farm"></a>Hosting w farmie internetowej

Aby uzyskać informacje na temat konfiguracji do hostowania aplikacji ASP.NET Core w środowisku kolektywu serwerów sieci Web (na przykład wdrażania wielu wystąpień aplikacji na potrzeby skalowalności), zobacz <xref:host-and-deploy/web-farm> .

## <a name="host-on-docker"></a>Host na platformie Docker

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/docker/index>.

## <a name="perform-health-checks"></a>Przeprowadzanie kontroli kondycji

Używaj oprogramowania do sprawdzania kondycji, aby przeprowadzać kontrole kondycji aplikacji i jej zależności. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/health-checks>.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:test/troubleshoot>
* [Hosting ASP.NET](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Ogólnie rzecz biorąc, aby wdrożyć aplikację ASP.NET Core w środowisku hostingu:

* Wdróż opublikowaną aplikację w folderze na serwerze hostingu.
* Skonfiguruj Menedżera procesów uruchamiający aplikację po nadejściu żądań i ponownym uruchomieniu aplikacji po awarii lub ponownym uruchomieniu serwera.
* W przypadku konfiguracji zwrotnego serwera proxy Skonfiguruj zwrotny serwer proxy do przesyłania dalej żądań do aplikacji.

## <a name="publish-to-a-folder"></a>Publikowanie w folderze

[Dotnet Publish](/dotnet/core/tools/dotnet-publish) polecenie kompiluje kod aplikacji i kopiuje pliki wymagane do uruchomienia aplikacji w folderze *publikacji* . Podczas wdrażania z programu Visual Studio, ten `dotnet publish` krok odbywa się automatycznie przed skopiowaniem plików do miejsca docelowego wdrożenia.

### <a name="folder-contents"></a>Zawartość folderu

Folder *publikowania* zawiera jeden lub więcej plików zestawów aplikacji, zależności i opcjonalnie środowiska uruchomieniowego platformy .NET.

Aplikację platformy .NET Core można opublikować jako *samodzielne wdrożenie* lub *wdrożenie zależne od platformy*. Jeśli aplikacja jest samodzielna, pliki zestawu, które zawierają środowisko uruchomieniowe platformy .NET, znajdują się w folderze *Publikowanie* . Jeśli aplikacja jest zależna od struktury, pliki środowiska uruchomieniowego platformy .NET nie są uwzględniane, ponieważ aplikacja ma odwołanie do wersji platformy .NET zainstalowanej na serwerze. Domyślny model wdrażania jest zależny od platformy. Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji .NET Core](/dotnet/core/deploying/).

Oprócz plików *exe* i *dll* , folder *publikacji* aplikacji ASP.NET Core zwykle zawiera pliki konfiguracji, zasoby statyczne i widoki MVC. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/directory-structure>.

## <a name="set-up-a-process-manager"></a>Konfigurowanie Menedżera procesów

Aplikacja ASP.NET Core jest aplikacją konsolową, która musi zostać uruchomiona w przypadku awarii i ponownego uruchomienia serwera. Aby zautomatyzować uruchamianie i ponowne uruchamianie, wymagany jest Menedżer procesów. Najbardziej typowymi menedżerami procesów dla ASP.NET Core są:

* Linux
  * [Nginx](xref:host-and-deploy/linux-nginx)
  * [Apache](xref:host-and-deploy/linux-apache)
* Windows
  * [IIS](xref:host-and-deploy/iis/index)
  * [Usługa systemu Windows](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>Konfigurowanie zwrotnego serwera proxy

Jeśli aplikacja używa serwera [Kestrel](xref:fundamentals/servers/kestrel) , usługi [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache)lub [IIS](xref:host-and-deploy/iis/index) mogą być używane jako zwrotny serwer proxy. Odwrotny serwer proxy odbiera żądania HTTP z Internetu i przekazuje je do usługi Kestrel.

Jest to &mdash; obsługiwana konfiguracja hostingu, która jest konfiguracją z serwerem odwrotnego serwera proxy lub bez niego &mdash; . Aby uzyskać więcej informacji, zobacz [Kiedy używać Kestrel z zwrotnym serwerem proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scenariusze serwera proxy i modułu równoważenia obciążenia

Dodatkowa konfiguracja może być wymagana dla aplikacji hostowanych w ramach serwerów proxy i modułów równoważenia obciążenia. Bez dodatkowej konfiguracji aplikacja może nie mieć dostępu do schematu (HTTP/HTTPS) i zdalnego adresu IP, z którego pochodzi żądanie. Aby uzyskać więcej informacji, zobacz [konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Korzystanie z programu Visual Studio i narzędzia MSBuild do automatyzowania wdrożeń

Wdrożenie często wymaga dodatkowych zadań oprócz kopiowania danych wyjściowych z [dotnet Publish](/dotnet/core/tools/dotnet-publish) na serwer. Na przykład dodatkowe pliki mogą być wymagane lub wykluczone z folderu *publikacji* . Program Visual Studio używa programu MSBuild do wdrażania w sieci Web, a programu MSBuild można dostosować do wykonywania wielu innych zadań podczas wdrażania. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/visual-studio-publish-profiles> i książka [using programu MSBuild i Team Foundation Build](http://msbuildbook.com/) .

Za pomocą [funkcji publikowania w sieci Web](xref:tutorials/publish-to-azure-webapp-using-vs) lub [wbudowanej obsługi narzędzia Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment)aplikacje można wdrażać bezpośrednio z programu Visual Studio do Azure App Service. Azure DevOps Services obsługuje [ciągłe wdrażanie do Azure App Service](/azure/devops/pipelines/targets/webapp). Aby uzyskać więcej informacji, zobacz [DevOps with ASP.NET Core i platformę Azure](xref:azure/devops/index).

## <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Zobacz, <xref:tutorials/publish-to-azure-webapp-using-vs> Aby uzyskać instrukcje dotyczące publikowania aplikacji na platformie Azure przy użyciu programu Visual Studio. Dodatkowy przykład jest zapewniany przez [utworzenie aplikacji sieci web ASP.NET Core na platformie Azure](/azure/app-service/app-service-web-get-started-dotnet).

## <a name="publish-with-msdeploy-on-windows"></a>Publikowanie z MSDeploy w systemie Windows

Zobacz <xref:host-and-deploy/visual-studio-publish-profiles> , aby uzyskać instrukcje dotyczące publikowania aplikacji przy użyciu profilu publikacji programu Visual Studio, w tym z wiersza polecenia systemu Windows przy użyciu polecenia [MSBuild programu dotnet](/dotnet/core/tools/dotnet-msbuild) .

## <a name="internet-information-services-iis"></a>Internet Information Services (IIS)

W przypadku wdrożeń do Internet Information Services (IIS) z konfiguracją dostarczoną przez plik *web.config* zapoznaj się z artykułami poniżej <xref:host-and-deploy/iis/index> .

## <a name="host-in-a-web-farm"></a>Hosting w farmie internetowej

Aby uzyskać informacje na temat konfiguracji do hostowania aplikacji ASP.NET Core w środowisku kolektywu serwerów sieci Web (na przykład wdrażania wielu wystąpień aplikacji na potrzeby skalowalności), zobacz <xref:host-and-deploy/web-farm> .

## <a name="host-on-docker"></a>Host na platformie Docker

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/docker/index>.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:test/troubleshoot>
* [Hosting ASP.NET](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
