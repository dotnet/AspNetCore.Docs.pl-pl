---
title: Publikowanie aplikacji ASP.NET Core w usługach IIS
author: rick-anderson
description: Dowiedz się, jak hostować aplikację ASP.NET Core na serwerze usług IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
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
uid: tutorials/publish-to-iis
ms.openlocfilehash: b3c714ea8e741430df1f70b2df258f1e8f1c7ad5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060511"
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

1. Uruchom ponownie serwer lub wykonaj `net stop was /y` `net start w3svc` polecenie w powłoce poleceń.

## <a name="create-the-iis-site"></a>Tworzenie witryny usług IIS

1. Na serwerze usług IIS Utwórz folder, który będzie zawierać opublikowane foldery i pliki aplikacji. W poniższym kroku ścieżka folderu jest dostarczana do usług IIS jako ścieżka fizyczna do aplikacji. Aby uzyskać więcej informacji na temat folderu wdrożenia aplikacji i układu pliku, zobacz <xref:host-and-deploy/directory-structure> .

1. W Menedżerze usług IIS Otwórz węzeł serwera w panelu **połączenia** . Kliknij prawym przyciskiem myszy folder **sitess** . Wybierz pozycję **Dodaj witrynę internetową** z menu kontekstowego.

1. Podaj **nazwę lokacji** i ustaw **ścieżkę fizyczną** do utworzonego folderu wdrożenia aplikacji. Podaj konfigurację **powiązania** i Utwórz witrynę sieci Web, wybierając **przycisk OK** .

   > [!WARNING]
   > `http://*:80/` `http://+:80` **Nie** należy używać powiązań z symbolami wieloznacznymi (i) najwyższego poziomu. Powiązania z symbolami wieloznacznymi najwyższego poziomu mogą otwierać aplikację pod kątem luk w zabezpieczeniach. Dotyczy to zarówno silnych, jak i słabych symboli wieloznacznych. Używaj jawnych nazw hostów zamiast symboli wieloznacznych. Powiązanie symboli wieloznacznych z poddomeną (na przykład `*.mysub.com` ) nie ma tego ryzyka bezpieczeństwa, jeśli kontrolujesz całą domenę nadrzędną (w przeciwieństwie do `*.com` , która jest narażona). Aby uzyskać więcej informacji, zobacz [sekcję rfc7230-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .

1. Potwierdź, że tożsamość modelu procesu ma odpowiednie uprawnienia.

   Jeśli domyślna tożsamość puli aplikacji ( **model procesów**  >  **Identity** ) została zmieniona z `ApplicationPoolIdentity` na inną tożsamość, sprawdź, czy Nowa tożsamość ma wymagane uprawnienia dostępu do folderu, bazy danych i innych wymaganych zasobów aplikacji. Na przykład Pula aplikacji wymaga dostępu do odczytu i zapisu do folderów, w których aplikacja odczytuje i zapisuje pliki.

## <a name="create-an-aspnet-core-no-locrazor-pages-app"></a>Tworzenie aplikacji ASP.NET Core Razor Pages

Postępuj zgodnie z <xref:getting-started> samouczkiem, aby utworzyć Razor aplikację Pages.

## <a name="publish-and-deploy-the-app"></a>Publikowanie i wdrażanie aplikacji

*Opublikowanie aplikacji* oznacza utworzenie skompilowanej aplikacji, która może być hostowana przez serwer programu. *Wdrożenie aplikacji* oznacza przeniesienie opublikowanej aplikacji do systemu hostingu. Krok Publikuj jest obsługiwany przez [zestaw .NET Core SDK](/dotnet/core/sdk), podczas gdy krok wdrożenia może być obsługiwany przez różne podejścia. Ten samouczek przyjmuje podejście do wdrożenia *folderu* , gdzie:
 
* Aplikacja zostanie opublikowana w folderze.
* Zawartość folderu zostanie przeniesiona do folderu witryny usług IIS ( **Ścieżka fizyczna** do lokacji w Menedżerze usług IIS).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **Publikuj** .
1. W oknie dialogowym **Wybieranie elementu docelowego publikowania** wybierz opcję Publikuj **folder** .
1. Ustaw ścieżkę **folderu lub udziału plików** .
   * Jeśli utworzono folder dla witryny usług IIS, która jest dostępna na komputerze deweloperskim jako udział sieciowy, podaj ścieżkę do udziału. Bieżący użytkownik musi mieć dostęp do zapisu w celu opublikowania w udziale.
   * Jeśli nie możesz wdrożyć bezpośrednio w folderze witryny usług IIS na serwerze IIS, Opublikuj je w folderze na nośniku wymiennym i fizycznie Przenieś opublikowaną aplikację do folderu witryny usług IIS na serwerze, który jest **ścieżką fizyczną** lokacji w Menedżerze usług IIS. Przenieś zawartość `bin/Release/{TARGET FRAMEWORK}/publish` folderu do folderu witryny usług IIS na serwerze, który jest **ścieżką fizyczną** tej witryny w Menedżerze usług IIS.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

1. W powłoce poleceń Opublikuj aplikację w konfiguracji wydania przy użyciu polecenia [dotnet Publish](/dotnet/core/tools/dotnet-publish) :

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. Przenieś zawartość `bin/Release/{TARGET FRAMEWORK}/publish` folderu do folderu witryny usług IIS na serwerze, który jest **ścieżką fizyczną** tej witryny w Menedżerze usług IIS.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Kliknij prawym przyciskiem myszy projekt w **rozwiązaniu** , a następnie wybierz pozycję **Publikuj**  >  **Publikowanie w folderze** .
1. Ustaw ścieżkę do **folderu wybierz** .
   * Jeśli utworzono folder dla witryny usług IIS, która jest dostępna na komputerze deweloperskim jako udział sieciowy, podaj ścieżkę do udziału. Bieżący użytkownik musi mieć dostęp do zapisu w celu opublikowania w udziale.
   * Jeśli nie możesz wdrożyć bezpośrednio w folderze witryny usług IIS na serwerze IIS, Opublikuj je w folderze na nośniku umożliwiającym usunięcie i fizycznie Przenieś opublikowaną aplikację do folderu witryny usług IIS na serwerze, który jest **ścieżką fizyczną** lokacji w Menedżerze usług IIS. Przenieś zawartość `bin/Release/{TARGET FRAMEWORK}/publish` folderu do folderu witryny usług IIS na serwerze, który jest **ścieżką fizyczną** tej witryny w Menedżerze usług IIS.

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

## <a name="additional-resources"></a>Dodatkowe zasoby

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

### <a name="deployment-resources-for-iis-administrators"></a>Zasoby wdrażania dla administratorów usług IIS

* [Dokumentacja usług IIS](/iis)
* [Wprowadzenie z menedżerem usług IIS w usługach IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Wdrażanie aplikacji .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

