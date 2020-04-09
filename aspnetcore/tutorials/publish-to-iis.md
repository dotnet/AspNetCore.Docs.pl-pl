---
title: Publikowanie aplikacji ASP.NET Core w ys
author: rick-anderson
description: Dowiedz się, jak hostować aplikację core ASP.NET na serwerze usług IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
uid: tutorials/publish-to-iis
ms.openlocfilehash: 47f78ba78741a8e0175ce801c0c0e51f091273a8
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511395"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a>Publikowanie aplikacji ASP.NET Core w ys

W tym samouczku pokazano, jak hostować aplikację core ASP.NET na serwerze usług IIS.

Ten samouczek obejmuje następujące tematy:

> [!div class="checklist"]
> * Zainstaluj pakiet hostingowy .NET Core w systemie Windows Server.
> * Utwórz witrynę usług IIS w Menedżerze usług IIS.
> * Wdrażanie aplikacji ASP.NET Core.

## <a name="prerequisites"></a>Wymagania wstępne

* [.NET Core SDK](/dotnet/core/sdk) zainstalowany na komputerze deweloperskim.
* System Windows Server skonfigurowany z rolą serwera **sieci Web (IIS).** Jeśli serwer nie jest skonfigurowany do hosta witryn sieci Web z usługami IIS, postępuj zgodnie ze wskazówkami w sekcji *konfiguracji usług IIS* w <xref:host-and-deploy/iis/index#iis-configuration> tym artykule, a następnie wróć do tego samouczka.

> [!WARNING]
> **Konfiguracja usług IIS i zabezpieczenia witryny sieci Web obejmują pojęcia, które nie są objęte tym samouczku.** Zapoznaj się ze wskazówkami usług IIS w [dokumentacji usług Microsoft IIS](https://www.iis.net/) i [artykułem ASP.NET Core na temat hostingu z usługami IIS](xref:host-and-deploy/iis/index) przed hostowanie aplikacji produkcyjnych na usługi IIS.
>
> Ważne scenariusze dla hostingu IIS nieobjęte tym samouczkiem obejmują:
>
> * [Tworzenie gałęzi rejestru dla ASP.NET Podstawowej Ochrony Danych](xref:host-and-deploy/iis/index#data-protection)
> * [Konfiguracja listy kontroli dostępu puli aplikacji (ACL)](xref:host-and-deploy/iis/index#application-pool-identity)
> * Aby skupić się na pojęciach wdrażania usług IIS, w tym samouczku wdraża się aplikacja bez zabezpieczeń HTTPS skonfigurowanych w serwisach IIS. Aby uzyskać więcej informacji na temat hostowania aplikacji włączonej dla protokołu HTTPS, zobacz tematy zabezpieczeń w sekcji [Dodatkowe zasoby](#additional-resources) tego artykułu. Dalsze wskazówki dotyczące hostingu aplikacji ASP.NET <xref:host-and-deploy/iis/index> Core znajdują się w artykule.

## <a name="install-the-net-core-hosting-bundle"></a>Zainstaluj pakiet hostingowy .NET Core

Zainstaluj *pakiet hostingu .NET Core* na serwerze usług IIS. Pakiet instaluje środowisko uruchomieniowe .NET Core, bibliotekę .NET Core library i [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module). Moduł umożliwia ASP.NET aplikacje Core działać za usługami IIS.

Pobierz instalatora za pomocą następującego linku:

[Aktualny instalator pakietu hostingu .NET Core (pobieranie bezpośrednie)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. Uruchom instalator na serwerze usług IIS.

1. Uruchom ponownie serwer lub wykonaj **net stop był /y,** a następnie **net start w3svc** w powiększe polecenia.

## <a name="create-the-iis-site"></a>Tworzenie witryny usługi IIS

1. Na serwerze usług IIS utwórz folder zawierający opublikowane foldery i pliki aplikacji. W następnym kroku ścieżka folderu jest dostarczana do iIS jako ścieżka fizyczna do aplikacji.

1. W Menedżerze usług IIS otwórz węzeł serwera w panelu **Połączenia.** Kliknij prawym przyciskiem myszy folder **Witryny.** Z menu kontekstowego **wybierz polecenie Dodaj witrynę sieci Web.**

1. Podaj **nazwę witryny** i ustaw **ścieżkę fizyczną** do utworzonego folderu wdrażania aplikacji. Podaj konfigurację **powiązania** i utwórz witrynę sieci Web, wybierając **przycisk OK**.

## <a name="create-an-aspnet-core-razor-pages-app"></a>Tworzenie aplikacji ASP.NET Core Razor Pages

Postępuj <xref:getting-started> zgodnie z samouczkiem, aby utworzyć aplikację Razor Pages.

## <a name="publish-and-deploy-the-app"></a>Publikowanie i wdrażanie aplikacji

*Publikowanie aplikacji* oznacza, aby utworzyć skompilowaną aplikację, która może być obsługiwana przez serwer. *Wdrażanie aplikacji* oznacza przeniesienie opublikowanej aplikacji do systemu hostingowego. Krok publikowania jest obsługiwany przez [.NET Core SDK](/dotnet/core/sdk), podczas gdy krok wdrażania może być obsługiwany przez różne podejścia. W tym samouczku przyjęto podejście do wdrażania *folderów,* gdzie:

* Aplikacja zostanie opublikowana w folderze.
* Zawartość folderu zostanie przeniesiona do folderu witryny usług IIS **(ścieżka fizyczna** do witryny w Menedżerze usług IIS).

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

1. Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz pozycję **Publikuj**.
1. W oknie **dialogowym Wybieranie celu publikacji** wybierz opcję **Publikowanie folderów.**
1. Ustaw ścieżkę **Folder lub File Share.**
   * Jeśli utworzono folder dla witryny IIS, który jest dostępny na komputerze deweloperskim jako udział sieciowy, podaj ścieżkę do udziału. Bieżący użytkownik musi mieć dostęp do zapisu, aby opublikować w udziale.
   * Jeśli nie możesz wdrożyć bezpośrednio w folderze witryny usług IIS na serwerze usług IIS, opublikuj w folderze na nośniku usuwalnym i fizycznie przenieś opublikowaną aplikację do folderu witryny usług IIS na serwerze, który jest **ścieżką fizyczną** witryny w Menedżerze usług IIS. Przenieś zawartość folderu *bin/release/{TARGET FRAMEWORK}/publish* do folderu lokacji usług IIS na serwerze, który jest **ścieżką fizyczną witryny** w Menedżerze usług IIS.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

1. W powłoce poleceń opublikuj aplikację w konfiguracji wydania za pomocą polecenia [dotnet publish:](/dotnet/core/tools/dotnet-publish)

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. Przenieś zawartość folderu *bin/release/{TARGET FRAMEWORK}/publish* do folderu lokacji usług IIS na serwerze, który jest **ścieżką fizyczną witryny** w Menedżerze usług IIS.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Kliknij prawym przyciskiem myszy projekt w **rozwiązaniu** i wybierz pozycję **Publikuj** > **w folderze**.
1. Ustaw **ścieżkę Wybierania folderu.**
   * Jeśli utworzono folder dla witryny IIS, który jest dostępny na komputerze deweloperskim jako udział sieciowy, podaj ścieżkę do udziału. Bieżący użytkownik musi mieć dostęp do zapisu, aby opublikować w udziale.
   * Jeśli nie możesz wdrożyć bezpośrednio w folderze witryny usług IIS na serwerze usług IIS, opublikuj w folderze na nośniku usuwalnym i fizycznie przenieś opublikowaną aplikację do folderu witryny usług IIS na serwerze, który jest **ścieżką fizyczną** witryny w Menedżerze usług IIS. Przenieś zawartość folderu *bin/release/{TARGET FRAMEWORK}/publish* do folderu lokacji usług IIS na serwerze, który jest **ścieżką fizyczną witryny** w Menedżerze usług IIS.

---

## <a name="browse-the-website"></a>Przeglądanie witryny sieci Web

Aplikacja jest dostępna w przeglądarce po otrzymaniu pierwszego żądania. Złożyć żądanie do aplikacji w powiązaniu punktu końcowego, które zostało ustanowione w Menedżerze usług IIS dla witryny.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zainstaluj pakiet hostingowy .NET Core w systemie Windows Server.
> * Utwórz witrynę usług IIS w Menedżerze usług IIS.
> * Wdrażanie aplikacji ASP.NET Core.

Aby dowiedzieć się więcej o hostingu aplikacji ASP.NET Core w serwisach IIS, zobacz artykuł Omówienie usług IIS:

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a>Zasoby dodatkowe

### <a name="articles-in-the-aspnet-core-documentation-set"></a>Artykuły w zestawie dokumentacji ASP.NET Core

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a>Artykuły dotyczące wdrażania aplikacji ASP.NET Core

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [Publikowanie aplikacji sieci Web w folderze przy użyciu programu Visual Studio dla komputerów Mac](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a>Artykuły dotyczące konfiguracji protokołu HTTPS usług IIS

* [Konfigurowanie ssl w menedżerze usług IIS](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [Jak skonfigurować ssl w serwisach IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a>Artykuły na usługach IIS i windows server

* [Oficjalna witryna microsoft iis](https://www.iis.net/)
* [Biblioteka zawartości technicznej systemu Windows Server](/windows-server/windows-server)
