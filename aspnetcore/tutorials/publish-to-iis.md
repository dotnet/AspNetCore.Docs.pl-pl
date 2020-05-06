---
title: Publikowanie aplikacji ASP.NET Core w usługach IIS
author: rick-anderson
description: Dowiedz się, jak hostować aplikację ASP.NET Core na serwerze usług IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/publish-to-iis
ms.openlocfilehash: aa79ce604539b4f09d6f17d4f43da28a6b615f53
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774577"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a>Publikowanie aplikacji ASP.NET Core w usługach IIS

W tym samouczku pokazano, jak hostować aplikację ASP.NET Core na serwerze usług IIS.

Ten samouczek obejmuje następujące zagadnienia:

> [!div class="checklist"]
> * Zainstaluj pakiet hostingu platformy .NET Core w systemie Windows Server.
> * Utwórz witrynę usług IIS w Menedżerze usług IIS.
> * Wdróż aplikację ASP.NET Core.

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestaw .NET Core SDK](/dotnet/core/sdk) zainstalowane na komputerze deweloperskim.
* System Windows Server skonfigurowany z rolą serwera **serwer sieci Web (IIS)** . Jeśli serwer nie jest skonfigurowany do hostowania witryn sieci Web za pomocą usług IIS, postępuj zgodnie ze wskazówkami w sekcji *Konfiguracja usług IIS* w <xref:host-and-deploy/iis/index#iis-configuration> artykule, a następnie wróć do tego samouczka.

> [!WARNING]
> **Konfiguracja usług IIS i zabezpieczenia witryny sieci Web obejmują pojęcia, które nie są objęte tym samouczkiem.** Zapoznaj się ze wskazówkami dotyczącymi usług IIS w [dokumentacji usług Microsoft IIS](https://www.iis.net/) i [artykułem ASP.NET Core na hostowanie usług IIS](xref:host-and-deploy/iis/index) przed hostowania aplikacji produkcyjnych w usługach IIS.
>
> Ważne scenariusze dotyczące hostingu usług IIS, które nie są objęte tym samouczkiem, obejmują:
>
> * [Tworzenie gałęzi rejestru na potrzeby ochrony danych ASP.NET Core](xref:host-and-deploy/iis/index#data-protection)
> * [Konfiguracja listy Access Control puli aplikacji (ACL)](xref:host-and-deploy/iis/index#application-pool-identity)
> * Aby skoncentrować się na pojęciach dotyczących wdrażania usług IIS, w tym samouczku wdrożono aplikację bez zabezpieczeń protokołu HTTPS skonfigurowanej w usługach IIS. Aby uzyskać więcej informacji na temat hostowania aplikacji z włączoną obsługą protokołu HTTPS, zobacz tematy dotyczące zabezpieczeń w sekcji [dodatkowe zasoby](#additional-resources) w tym artykule. Dalsze wskazówki dotyczące hostingu ASP.NET Core aplikacji znajdują się w <xref:host-and-deploy/iis/index> artykule.

## <a name="install-the-net-core-hosting-bundle"></a>Zainstaluj pakiet hostingu platformy .NET Core

Zainstaluj *pakiet hostingu platformy .NET Core* na serwerze usług IIS. Pakiet instaluje środowisko uruchomieniowe programu .NET Core, bibliotekę .NET Core i [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Moduł umożliwia uruchamianie aplikacji ASP.NET Core za pomocą usług IIS.

Pobierz instalatora przy użyciu następującego linku:

[Bieżący Instalator pakietu hostingu platformy .NET Core (Pobieranie bezpośrednie)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. Uruchom Instalatora na serwerze usług IIS.

1. Uruchom ponownie serwer, a **następnie wykonaj polecenie** **net start W3SVC** w powłoce poleceń.

## <a name="create-the-iis-site"></a>Tworzenie witryny usług IIS

1. Na serwerze usług IIS Utwórz folder, który będzie zawierać opublikowane foldery i pliki aplikacji. W poniższym kroku ścieżka folderu jest dostarczana do usług IIS jako ścieżka fizyczna do aplikacji.

1. W Menedżerze usług IIS Otwórz węzeł serwera w panelu **połączenia** . Kliknij prawym przyciskiem myszy folder **sitess** . Wybierz pozycję **Dodaj witrynę internetową** z menu kontekstowego.

1. Podaj **nazwę lokacji** i ustaw **ścieżkę fizyczną** do utworzonego folderu wdrożenia aplikacji. Podaj konfigurację **powiązania** i Utwórz witrynę sieci Web, wybierając **przycisk OK**.

## <a name="create-an-aspnet-core-razor-pages-app"></a>Tworzenie aplikacji Razor Pages ASP.NET Core

Postępuj <xref:getting-started> zgodnie z samouczkiem, aby utworzyć aplikację Razor Pages.

## <a name="publish-and-deploy-the-app"></a>Publikowanie i wdrażanie aplikacji

*Opublikowanie aplikacji* oznacza utworzenie skompilowanej aplikacji, która może być hostowana przez serwer programu. *Wdrożenie aplikacji* oznacza przeniesienie opublikowanej aplikacji do systemu hostingu. Krok Publikuj jest obsługiwany przez [zestaw .NET Core SDK](/dotnet/core/sdk), podczas gdy krok wdrożenia może być obsługiwany przez różne podejścia. Ten samouczek przyjmuje podejście do wdrożenia *folderu* , gdzie:

* Aplikacja zostanie opublikowana w folderze.
* Zawartość folderu zostanie przeniesiona do folderu witryny usług IIS ( **Ścieżka fizyczna** do lokacji w Menedżerze usług IIS).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **Publikuj**.
1. W oknie dialogowym **Wybieranie elementu docelowego publikowania** wybierz opcję Publikuj **folder** .
1. Ustaw ścieżkę **folderu lub udziału plików** .
   * Jeśli utworzono folder dla witryny usług IIS, która jest dostępna na komputerze deweloperskim jako udział sieciowy, podaj ścieżkę do udziału. Bieżący użytkownik musi mieć dostęp do zapisu w celu opublikowania w udziale.
   * Jeśli nie możesz wdrożyć bezpośrednio w folderze witryny usług IIS na serwerze IIS, Opublikuj je w folderze na nośniku umożliwiającym usunięcie i fizycznie Przenieś opublikowaną aplikację do folderu witryny usług IIS na serwerze, który jest **ścieżką fizyczną** lokacji w Menedżerze usług IIS. Przenieś zawartość folderu *bin/Release/{Target Framework}/Publish* do folderu witryny usług IIS na serwerze, który jest **ścieżką fizyczną** lokacji w Menedżerze usług IIS.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

1. W powłoce poleceń Opublikuj aplikację w konfiguracji wydania przy użyciu polecenia [dotnet Publish](/dotnet/core/tools/dotnet-publish) :

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. Przenieś zawartość folderu *bin/Release/{Target Framework}/Publish* do folderu witryny usług IIS na serwerze, który jest **ścieżką fizyczną** lokacji w Menedżerze usług IIS.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Kliknij prawym przyciskiem myszy projekt w **rozwiązaniu** , a następnie wybierz pozycję **Publikuj** > **Publikowanie w folderze**.
1. Ustaw ścieżkę do **folderu wybierz** .
   * Jeśli utworzono folder dla witryny usług IIS, która jest dostępna na komputerze deweloperskim jako udział sieciowy, podaj ścieżkę do udziału. Bieżący użytkownik musi mieć dostęp do zapisu w celu opublikowania w udziale.
   * Jeśli nie możesz wdrożyć bezpośrednio w folderze witryny usług IIS na serwerze IIS, Opublikuj je w folderze na nośniku umożliwiającym usunięcie i fizycznie Przenieś opublikowaną aplikację do folderu witryny usług IIS na serwerze, który jest **ścieżką fizyczną** lokacji w Menedżerze usług IIS. Przenieś zawartość folderu *bin/Release/{Target Framework}/Publish* do folderu witryny usług IIS na serwerze, który jest **ścieżką fizyczną** lokacji w Menedżerze usług IIS.

---

## <a name="browse-the-website"></a>Przeglądanie witryny sieci Web

Aplikacja jest dostępna w przeglądarce po odebraniu pierwszego żądania. Prześlij żądanie do aplikacji przy użyciu powiązania punktu końcowego, który został ustanowiony w Menedżerze usług IIS dla tej lokacji.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zainstaluj pakiet hostingu platformy .NET Core w systemie Windows Server.
> * Utwórz witrynę usług IIS w Menedżerze usług IIS.
> * Wdróż aplikację ASP.NET Core.

Aby dowiedzieć się więcej na temat hostowania aplikacji ASP.NET Core w usługach IIS, zobacz artykuł Omówienie usług IIS:

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
* [Publikowanie aplikacji sieci Web w folderze przy użyciu Visual Studio dla komputerów Mac](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a>Artykuły na temat konfiguracji protokołu HTTPS usług IIS

* [Konfigurowanie protokołu SSL w Menedżerze usług IIS](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [Jak skonfigurować protokół SSL w usługach IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a>Artykuły dotyczące usług IIS i systemu Windows Server

* [Oficjalna witryna programu Microsoft IIS](https://www.iis.net/)
* [Biblioteka zawartości technicznej systemu Windows Server](/windows-server/windows-server)
